<template>
  <div>
    <div class="title fixed-title" :style="{background: headerColor, color: getTextColor(headerColor)}">
      <h1>Talonario</h1>
    </div>
    <div class="container with-title-padding">
      <datatalonario ref="modalTalonario" @update-datos="actualizarDatos" />
      <carddatos
        :premio="datosCard.premio"
        :precio="datosCard.precio"
        :tipo="datosCard.tipo"
        :cantidad="datosCard.cantidad"
        :fecha="datosCard.fecha"
        @editar="editarTalonario"
        @update-header-color="actualizarHeaderColor"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import datatalonario from './components/datatalonario.vue';
import carddatos from './components/carddatos.vue';

const headerColor = ref('#601f9e');

function actualizarHeaderColor(nuevoColor) {
  headerColor.value = nuevoColor;
}

function getTextColor(bgColor) {
  if (!bgColor) return '#fff';
  const hex = bgColor.replace('#', '');
  const r = parseInt(hex.substring(0,2), 16);
  const g = parseInt(hex.substring(2,4), 16);
  const b = parseInt(hex.substring(4,6), 16);
  const luminance = 0.299*r + 0.587*g + 0.114*b;
  return luminance > 180 ? '#222' : '#fff';
}
const datosCard = ref({
  premio: '',
  precio: '',
  tipo: '',
  cantidad: '',
  fecha: ''
});

const modalTalonario = ref(null);

function actualizarDatos(nuevosDatos) {
  datosCard.value = { ...nuevosDatos };
}

function editarTalonario() {
  if (modalTalonario.value && modalTalonario.value.openModal) {
    modalTalonario.value.openModal(datosCard.value);
  }
}
</script>

<style scoped>
h1{
  margin: 0;
}
.title {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px 50px;
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
}
.fixed-title {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 500;
}
.with-title-padding {
  padding-top: 90px;
}
h1{
  font-size: 50px;
}
</style>