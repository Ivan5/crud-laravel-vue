<template>
  <div>
    <form @submit.prevent="editarNota(nota)" v-if="editarActivo">
      <h3>Editar Tareas</h3>
      <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="tarea.nombre">
      <input
        type="text"
        placeholder="Descripcion"
        class="form-control mb-2"
        v-model="tarea.descripcion"
      >
      <button type="submit" class="btn btn-warning">Editar</button>
    </form>

    <form @submit.prevent="agregar" v-else>
      <h3>Agregar Tareas</h3>
      <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="tarea.nombre">
      <input
        type="text"
        placeholder="Descripcion"
        class="form-control mb-2"
        v-model="tarea.descripcion"
      >
      <button type="submit" class="btn btn-primary">Agregar</button>
    </form>

    <hr class="mt-3">
    <h3>Listado de notas</h3>
    <ul class="list-group my-2">
      <li class="list-group-item" v-for="(item,index) in tareas" :key="index">
        <span class="badge badge-primary float-right">{{item.updated_at}}</span>
        <p>{{item.nombre}}</p>
        <p>{{item.descripcion}}</p>
        <button class="btn btn-danger btn-sm" @click="eliminarNota(item,index)">Eliminar</button>
        <button class="btn btn-warning btn-sm" @click="editarFormulario(item)">Editar</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tareas: [],
      tarea: {
        nombe: "",
        descripcion: ""
      },
      editarActivo: false
    };
  },
  created() {
    axios.get("/notas").then(res => {
      this.tareas = res.data;
    });
  },
  methods: {
    agregar() {
      if (
        this.tarea.nombre.trim() === "" ||
        this.tarea.descripcion.trim() === ""
      ) {
        alert("Debes completar todos los campos antes de guardar");
        return;
      }

      const params = {
        nombre: this.tarea.nombre,
        descripcion: this.tarea.descripcion
      };
      this.tarea.nombre = "";
      this.tarea.descripcion = "";
      axios.post("/notas", params).then(res => {
        this.tareas.push(res.data);
      });
    },
    eliminarNota(item, index) {
      axios.delete(`/notas/${item.id}`).then(() => {
        this.tareas.splice(index, 1);
      });
    },
    editarFormulario(item) {
      this.editarActivo = true;
      this.tarea.nombre = item.nombre;
      this.tarea.descripcion = item.descripcion;
      this.tare.id = item.id;
    }
  },
  editarNota(item) {
    const params = {
      nombre: item.nombre,
      descripcion: item.descripcion
    };

    axios.put(`/notas/${item.id}`, params).then(res => {
      const index = this.tareas.findIndex(
        notaBuscar => notaBuscar.id === res.data.id
      );
      this.tareas[index] = res.data;
    });
  }
};
</script>

<style>
</style>
