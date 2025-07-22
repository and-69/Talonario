<template>
  <div ref="modalElement" class="modal fade" id="staticBackdrop" tabindex="-1" v-show="showModal" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Configuración del Talonario</h5>
          <button type="button" class="btn-close" @click="close"></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-md-12">
              <input type="number" class="form-control" v-model="premioRifa" placeholder="Premio del talonario">
            </div>
          </div>
          <div class="row">
            <div class="col-md-12">
              <input type="number" class="form-control" v-model="precioBoleta" placeholder="Precio de la boleta">
            </div>
          </div>
          <div class="row">
            <div class="col-md-12">
              <select class="form-select" aria-label="Default select example" v-model="tipoLoteria">
                <option value="" selected disabled>Loteria</option>
                <option value="La Perla">La Perla</option>
                <option value="Baloto">Baloto</option>
                <option value="Culona">Culona</option>
                <option value="Santander">Santander</option>
                <option value="Chance">Chance</option>
              </select>
            </div>
          </div>
          <div class="row">
            <div class="col-md-12">
              <select class="form-select" aria-label="Default select example" v-model="cantidadBoletas">
                <option value="" selected disabled>Boletas</option>
                <option value="99">0-99</option>
                <option value="999">0-999</option>
                <option value="9999">0-9999</option>
              </select>
            </div>
          </div>
          <div class="row">
            <div class="col-md-12">
              <input type="date" class="form-control" v-model="fechaSorteo" :min="getMinFechaSorteo()"
                placeholder="Fecha del sorteo">
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-outline-light" @click="guardar()">Guardar</button>
          <button v-if="esEdicion" type="button" class="btn btn-outline-light" @click="closeModal">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, defineEmits } from 'vue';

const emit = defineEmits(['update-datos']);

const showModal = ref(true);
const modalElement = ref(null);
const premioRifa = ref('');
const precioBoleta = ref('');
const tipoLoteria = ref('');
const cantidadBoletas = ref('');
const fechaSorteo = ref('');
const esEdicion = ref(false);

let modalInstance = null;
const openModal = (datos = null) => {
  if (datos) {
    premioRifa.value = datos.premio || '';
    precioBoleta.value = datos.precio || '';
    tipoLoteria.value = datos.tipo || '';
    cantidadBoletas.value = datos.cantidad || '';
    fechaSorteo.value = datos.fecha || '';
    esEdicion.value = true;
  } else {
    esEdicion.value = false;
  }
  showModal.value = true;
  if (!modalInstance) {
    modalInstance = new bootstrap.Modal(modalElement.value, {
      backdrop: 'static',
    });
  }
  modalInstance.show();
};

defineExpose({ openModal });
const close = () => {
  Swal.fire({
    icon: 'error',
    title: 'Opps!',
    text: 'Debes completar todos los campos antes de continuar.',
  });
  showModal.value = true;

};
const closeModal = () => {
  showModal.value = false;
  if (modalInstance) {
    modalInstance.hide();
  }
};
const minDays = 8
function getMinFechaSorteo() {
  const hoy = new Date();
  hoy.setDate(hoy.getDate() + minDays);
  return hoy.toISOString().split('T')[0];
}
const validaciones = () => {
  const premio = Number(premioRifa.value);
  const precio = Number(precioBoleta.value);
  const cantidad = Number(cantidadBoletas.value);
  const tipo = tipoLoteria.value;
  const fecha = fechaSorteo.value;

  if (premio < cantidad * precio) {
    Swal.fire({
      icon: 'error',
      title: 'Error',
      text: 'El premio no puede ser menor al total de las boletas vendidas.',
    });
    return false;
  } else if (!premio || !precio || !cantidad || !tipo || !fecha) {
    Swal.fire({
      icon: 'warning',
      title: 'Campos incompletos',
      text: 'Por favor, completa todos los campos.',
    });
    return false;
  }
  return true;
}

const guardar = () => {
  if (validaciones()) {
    Swal.fire({
      icon: 'success',
      title: 'Éxito',
      text: 'Configuración guardada correctamente.',
    });
    const datos = {
      premio: premioRifa.value,
      precio: precioBoleta.value,
      tipo: tipoLoteria.value,
      cantidad: cantidadBoletas.value,
      fecha: fechaSorteo.value
    };
    emit('update-datos', datos);
    closeModal();
  }
};

onMounted(() => {
  openModal();
});
</script>
<style scoped>
.modal-content {
  font-size: 20px;
}

.modal-header {
  background-color: rgb(96, 31, 158);
}

.modal-title {
  font-size: 28px;
  color: white;
}

.modal-body {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;

}

.form-control,
.form-select {
  font-size: 20px;
  padding: 10px 8px;
  height: auto;
}

.modal-footer .btn {
  font-size: 20px;
  padding: 8px 18px;
}

.modal-footer {
  background-color: rgb(96, 31, 158);
}
.modal-content{
      border: 2px solid #601f9e;
}
</style>