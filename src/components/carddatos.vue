<template>
    <div>
        <div class="modal fade" id="modalConfig" tabindex="-1" aria-labelledby="modalConfigLabel" aria-hidden="true">
            <div class="modal-dialog modal-lg modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header" :style="{background: colorHeader, color: getTextColor(colorHeader)}">
                        <h4 class="modal-title w-100 text-center" id="modalConfigLabel">Temas Para El Talonario</h4>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="row mb-4">
                            <h3 class="w-100 text-center" style="font-family:'Courier New', Courier, monospace;">Colores Header o Encabezados
                            </h3>
                            <div class="d-flex justify-content-center gap-3 flex-wrap">
                                <label v-for="(c, i) in colores" :key="'header'+i" class="color-radio">
                                    <input type="radio" name="header" :value="c.valor" v-model="colorHeader">
                                    <span>{{c.nombre}}</span>
                                    <span class="color-dot" :style="{background:c.valor}"></span>
                                </label>
                            </div>
                        </div>
                        <div class="row mb-4">
                            <h3 class="w-100 text-center" style="font-family:'Courier New', Courier, monospace;">Colores Botones</h3>
                            <div class="d-flex justify-content-center gap-3 flex-wrap">
                                <label v-for="(c, i) in colores" :key="'boton'+i" class="color-radio">
                                    <input type="radio" name="boton" :value="c.valor" v-model="colorBoton">
                                    <span>{{c.nombre}}</span>
                                    <span class="color-dot" :style="{background:c.valor}"></span>
                                </label>
                            </div>
                        </div>
                        <div class="row mb-4">
                          <h3 class="w-100 text-center" style="font-family:'Courier New', Courier, monospace;">Color Números Talonario</h3>
                          <div class="d-flex justify-content-center gap-3 flex-wrap">
                            <label v-for="(c, i) in colores" :key="'num'+i" class="color-radio">
                              <input type="radio" name="num" :value="c.valor" v-model="colorNumero">
                              <span>{{c.nombre}}</span>
                              <span class="color-dot" :style="{background:c.valor}"></span>
                            </label>
                          </div>
                        </div>
                        <div class="row mt-4">
                            <div class="col text-center">
                                <button class="btn btn-primary me-2" @click="setDefaultColors" :style="{background: colorHeader, borderColor: colorHeader}">Colores por Defecto</button>
                                <button class="btn btn-danger" data-bs-dismiss="modal">Cerrar</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="card mt-3">
            <div class="card-body text-center">
                <h5 class="card-title">Informacion del talonario</h5>
                <p><strong>Premio:</strong> {{ premio }}</p>
                <p><strong>Precio Boleta:</strong> {{ precio }}</p>
                <p><strong>Lotería:</strong> {{ tipo }}</p>
                <p><strong>Cantidad Boletas:</strong> {{ cantidad }}</p>
                <p><strong>Fecha Sorteo:</strong> {{ fecha }}</p>
                <button class="btn btn-dark" @click="$emit('editar')" :style="{background: colorBoton, borderColor: colorBoton}"><i class="bi bi-pencil-fill"></i></button>
                <div class="hr"></div>
                <div class="list-config">
                    <button class="btn btn-dark" @click="abrirModalListado" :style="{background: colorBoton, borderColor: colorBoton}"><i class="bi bi-card-list"></i></button>
                    <button class="btn btn-dark" @click="abrirModalConfig" :style="{background: colorBoton, borderColor: colorBoton}"><i class="bi bi-gear"></i></button>
                </div>
                <div class="hr"></div>
                <div class="winner">
                    <button class="btn btn-dark" @click="generarGanador" :style="{background: colorBoton, borderColor: colorBoton}"><i class="bi bi-trophy-fill"></i></button>
                    <button class="btn btn-dark ms-2" @click="elegirGanadorPersonalizado" :style="{background: colorBoton, borderColor: colorBoton}"><i class="bi bi-person-check"></i></button>
                </div>
                <div class="modal fade" id="modalListado" tabindex="-1" aria-labelledby="modalListadoLabel" aria-hidden="true">
                    <div class="modal-dialog modal-lg modal-dialog-centered">
                        <div class="modal-content">
                            <div class="modal-header" :style="{background: colorHeader, color: getTextColor(colorHeader)}">
                                <h4 class="modal-title" id="modalListadoLabel">Listado de Boleta</h4>
                                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div class="modal-body">
                                <div class="factura-box table-responsive-custom">
                                    <table class="table table-bordered text-center">
                                        <thead>
                                            <tr style="background:#1976d2;color:#fff;">
                                                <th>Nombre Comprador</th>
                                                <th>Teléfono</th>
                                                <th>Estado</th>
                                                <th>N. Boleta</th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr v-for="(item, idx) in listadoBoletas" :key="item.numero">
                                                <td>{{ item.nombre }}</td>
                                                <td>{{ item.telefono }}</td>
                                                <td>
                                                    <template v-if="item.estado === 'Apartada'">
                                                        <select v-model="item.estado" @change="actualizarEstado(idx, item.estado)" class="form-select form-select-sm" style="width:110px;">
                                                            <option value="Pagada">Pagada</option>
                                                            <option value="Apartada">Apartada</option>
                                                        </select>
                                                    </template>
                                                    <template v-else>
                                                        {{ item.estado }}
                                                    </template>
                                                </td>
                                                <td>{{ item.numero }}</td>
                                            </tr>
                                        </tbody>
                                    </table>
                                    <div v-if="listadoBoletas.length === 0" class="text-center text-muted">No hay boletas adquiridas.</div>
                                    <div class="row mt-3">
                                        <div class="col text-end">
                                            <button class="btn btn-success" @click="exportarPDF"><i class="bi bi-file-earmark-pdf"></i></button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div v-if="boletas.length" class="card mt-3">
            <div class="card-body text-center">
                <h5 class="card-title">Talonario</h5>
                <div class="talonario-list">
                    <div v-for="num in getBoletas()" :key="num"
                        :class="['boleta-circle', adquiridas.includes(num) ? 'adquirida' : '']"
                        :style="{background: colorNumero}"
                        @click="abrirModal(num)">
                        {{ num }}
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="modalBoleta" tabindex="-1" aria-labelledby="modalBoletaLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header" :style="{background: colorHeader, color: getTextColor(colorHeader)}">
                        <h5 class="modal-title" id="modalBoletaLabel">Boleta {{ boletaSeleccionada }}</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body text-center">
                        <p><strong>Estado:</strong> <span
                                :style="{ color: estadoBoleta === 'Disponible' ? '#1e2a78' : '#3abe4c' }">{{ estadoBoleta
                                }}</span>
                        </p>
                    </div>
                    <div class="modal-footer d-block text-center">
                        <button class="btn btn-primary w-100" @click="abrirModalDatos"
                            :disabled="estadoBoleta !== 'Disponible'"
                            :style="{background: colorBoton, borderColor: colorBoton}">
                            <i class="bi bi-bag"></i>
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="modalDatos" tabindex="-1" aria-labelledby="modalDatosLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header" :style="{background: colorHeader, color: getTextColor(colorHeader)}">
                        <h5 class="modal-title" id="modalDatosLabel">Datos del comprador</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <form @submit.prevent="guardarDatos">
                        <div class="modal-body">
                            <div class="mb-3">
                                <label class="form-label">Nombre</label>
                                <input type="text" v-model="datosComprador.nombre" class="form-control">
                            </div>
                            <div class="mb-3">
                                <label class="form-label">Teléfono</label>
                                <input type="text" v-model="datosComprador.telefono" class="form-control">
                            </div>
                            <div class="mb-3">
                                <label class="form-label">Estado</label>
                                <div class="form-check">
                                    <input class="form-check-input" type="radio" v-model="datosComprador.estado"
                                        value="Pagada" id="pagada">
                                    <label class="form-check-label" for="pagada">Pagada</label>
                                </div>
                                <div class="form-check">
                                    <input class="form-check-input" type="radio" v-model="datosComprador.estado"
                                        value="Apartada" id="apartada">
                                    <label class="form-check-label" for="apartada">Apartada</label>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer d-block text-center">
                            <button type="submit" class="btn btn-primary w-100" :style="{background: colorBoton, borderColor: colorBoton}"><i class="bi bi-bag"></i></button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>

</template>

<script setup>
import { ref, computed, watch } from 'vue';
const emit = defineEmits(['update-header-color']);
const props = defineProps({
    premio: String,
    precio: String,
    tipo: String,
    cantidad: String,
    fecha: String
});
function generarGanador() {
    if (ganadorSorteo.value) {
        SweetAlert.fire({
            icon: 'info',
            title: 'Ya hay un ganador!',
            html: `<b>${ganadorSorteo.value.nombre}</b><br>Boleta: <b>${ganadorSorteo.value.numero}</b><br>Teléfono: <b>${ganadorSorteo.value.telefono}</b>`,
            confirmButtonColor: colorHeader.value
        });
        return;
    }
    if (listadoBoletas.value.length < 5) {
        SweetAlert.fire({
            icon: 'info',
            title: 'No hay suficientes boletas adquiridas',
            text: 'Debe haber minimo 5 boletas adquiridas para sortear el premio.'
        });
        return;
    }
    const idx = Math.floor(Math.random() * listadoBoletas.value.length);
    const ganador = listadoBoletas.value[idx];
    ganadorSorteo.value = ganador;
    SweetAlert.fire({
        icon: 'success',
        title: '¡Ganador!',
        html: `<b>${ganador.nombre}</b><br>Boleta: <b>${ganador.numero}</b><br>Teléfono: <b>${ganador.telefono}</b>`,
        confirmButtonColor: colorHeader.value
    });
}

function elegirGanadorPersonalizado() {
    if (ganadorSorteo.value) {
        SweetAlert.fire({
            icon: 'info',
            title: 'Ya hay un ganador!',
            html: `<b>${ganadorSorteo.value.nombre}</b><br>Boleta: <b>${ganadorSorteo.value.numero}</b><br>Teléfono: <b>${ganadorSorteo.value.telefono}</b>`,
            confirmButtonColor: colorHeader.value
        });
        return;
    }
    
    if (listadoBoletas.value.length === 0) {
        SweetAlert.fire({
            icon: 'info',
            title: 'No hay boletas adquiridas',
            text: 'Debe haber al menos una boleta adquirida para elegir ganador.'
        });
        return;
    }

    // Crear lista de números disponibles para el select
    const opcionesNumeros = listadoBoletas.value.map(boleta => 
        `<option value="${boleta.numero}">${boleta.numero} - ${boleta.nombre}</option>`
    ).join('');

    SweetAlert.fire({
        title: 'Elegir Ganador Personalizado',
        html: `
            <label for="numero-ganador" style="display: block; margin-bottom: 10px; font-weight: bold;">Selecciona el número ganador:</label>
            <select id="numero-ganador" class="swal2-input" style="width: 100%; padding: 10px; font-size: 16px; border: 1.5px solid #601f9e; border-radius: 8px;">
                <option value="">Seleccionar número...</option>
                ${opcionesNumeros}
            </select>
        `,
        showCancelButton: true,
        confirmButtonText: 'Elegir Ganador',
        cancelButtonText: 'Cancelar',
        confirmButtonColor: colorHeader.value,
        cancelButtonColor: '#6c757d',
        customClass: {
            popup: 'custom-swal-popup',
            title: 'custom-swal-title',
            content: 'custom-swal-content'
        },
        preConfirm: () => {
            const numeroSeleccionado = document.getElementById('numero-ganador').value;
            if (!numeroSeleccionado) {
                SweetAlert.showValidationMessage('Debes seleccionar un número');
                return false;
            }
            return numeroSeleccionado;
        }
    }).then((result) => {
        if (result.isConfirmed) {
            const numeroGanador = result.value;
            const ganador = listadoBoletas.value.find(boleta => boleta.numero === numeroGanador);
            
            if (ganador) {
                ganadorSorteo.value = ganador;
                SweetAlert.fire({
                    icon: 'success',
                    title: '¡Ganador Elegido!',
                    html: `<b>${ganador.nombre}</b><br>Boleta: <b>${ganador.numero}</b><br>Teléfono: <b>${ganador.telefono}</b>`,
                    confirmButtonColor: colorHeader.value
                });
            }
        }
    });
}

function actualizarEstado(idx, nuevoEstado) {
    listadoBoletas.value[idx].estado = nuevoEstado;
}

function exportarPDF() {
    const jsPDF = window.jspdf?.jsPDF;
    const autoTable = window.jspdf?.jsPDF?.API?.autoTable;
    if (!jsPDF || !autoTable) {
        SweetAlert.fire({
            icon: 'error',
            title: 'Error',
            text: 'No se encontró jsPDF o AutoTable. Verifica el CDN.',
        });
        return;
    }
    const doc = new jsPDF();
    doc.setFont('courier', 'bold');
    doc.setFontSize(18);
    doc.text('Factura - Listado de Boletas', 14, 18);
    doc.setFontSize(12);
    doc.text(`Premio: ${props.premio || ''}`, 14, 28);
    doc.text(`Fecha Sorteo: ${props.fecha || ''}`, 14, 36);
    doc.text(`Lotería: ${props.tipo || ''}`, 14, 44);
    doc.text(`Precio Boleta: ${props.precio || ''}`, 14, 52);
    autoTable.call(doc, {
        startY: 60,
        head: [['Nombre', 'Teléfono', 'Estado', 'N. Boleta']],
        body: listadoBoletas.value.map(b => [b.nombre, b.telefono, b.estado, b.numero]),
        theme: 'grid',
        headStyles: { fillColor: [25, 118, 210], textColor: 255, fontStyle: 'bold', fontSize: 12 },
        bodyStyles: { fontSize: 11 },
        styles: { font: 'courier' },
    });
    doc.save('Talonario.pdf');
}


function getBoletas() {
    const cant = parseInt(props.cantidad);
    if (!cant || isNaN(cant)) return [];
    
    // Determinar el número de dígitos necesarios
    const digitos = cant.toString().length;
    
    return Array.from({ length: cant + 1 }, (_, i) => {
        return i.toString().padStart(digitos, '0');
    });
}

const boletas = computed(getBoletas);

const adquiridas = ref([]);
const listadoBoletas = ref([]);
const boletaSeleccionada = ref(null);
const estadoBoleta = ref('Disponible');

const datosComprador = ref({ nombre: '', telefono: '', estado: '' });
const ganadorSorteo = ref(null);

function abrirModal(num) {
    boletaSeleccionada.value = num;
    estadoBoleta.value = adquiridas.value.includes(num) ? 'Adquirida' : 'Disponible';
    const modal = window.bootstrap?.Modal ? new window.bootstrap.Modal(document.getElementById('modalBoleta')) : null;
    if (modal) modal.show();
}

function abrirModalDatos() {
    datosComprador.value = { nombre: '', telefono: '', estado: '' };
    const modal = window.bootstrap?.Modal ? new window.bootstrap.Modal(document.getElementById('modalDatos')) : null;
    if (modal) modal.show();
    const modalBoleta = window.bootstrap?.Modal ? window.bootstrap.Modal.getInstance(document.getElementById('modalBoleta')) : null;
    if (modalBoleta) modalBoleta.hide();
}

function guardarDatos() {
    if (!datosComprador.value.nombre.trim()) {
        SweetAlert.fire({
            icon: 'error',
            title: 'Opps!',
            text: 'Por favor ingresa el nombre del comprador.',
        });
        return;
    }
    if (!datosComprador.value.telefono.trim()|| datosComprador.value.telefono.length < 10) {
        SweetAlert.fire({
            icon: 'error',
            title: 'Opps!',
            text: 'Por favor ingresa el teléfono del comprador, mínimo 10 caracteres.',
        });
        return;
    }
    if (!adquiridas.value.includes(boletaSeleccionada.value)) {
        adquiridas.value.push(boletaSeleccionada.value);
        estadoBoleta.value = 'Adquirida';
        listadoBoletas.value.push({
            nombre: datosComprador.value.nombre,
            telefono: datosComprador.value.telefono,
            estado: datosComprador.value.estado,
            numero: boletaSeleccionada.value
        });
        SweetAlert.fire({
            icon: 'success',
            title: '¡Guardado!',
            text: 'Datos de la boleta guardados correctamente.',
            confirmButtonColor: '#601f9e',
            timer: 1500
        });
    }
    const modal = window.bootstrap?.Modal ? window.bootstrap.Modal.getInstance(document.getElementById('modalDatos')) : null;
    if (modal) modal.hide();
}

function abrirModalListado() {
    const modal = window.bootstrap?.Modal ? new window.bootstrap.Modal(document.getElementById('modalListado')) : null;
    if (modal) modal.show();
}

const colores = [
  {nombre: 'Color Amarillo', valor: '#FFD600'},
  {nombre: 'Color Verde', valor: '#4CAF50'},
  {nombre: 'Color Rosa', valor: '#C2185B'},
  {nombre: 'Color Naranja', valor: '#FFB74D'}
];
const colorHeader = ref('#601f9e');
const colorBoton = ref('#601f9e');
const colorNumero = ref('#601f9e');
function setDefaultColors() {
  colorHeader.value = '#601f9e';
  colorBoton.value = '#601f9e';
  colorNumero.value = '#601f9e';
}
function abrirModalConfig() {
  const modal = window.bootstrap?.Modal ? new window.bootstrap.Modal(document.getElementById('modalConfig')) : null;
  if (modal) modal.show();
}
watch(colorHeader, (nuevo) => {
  emit('update-header-color', nuevo);
});

function getTextColor(bgColor) {
  if (!bgColor) return '#fff';
  const hex = bgColor.replace('#', '');
  const r = parseInt(hex.substring(0,2), 16);
  const g = parseInt(hex.substring(2,4), 16);
  const b = parseInt(hex.substring(4,6), 16);
  const luminance = 0.299*r + 0.587*g + 0.114*b;
  return luminance > 180 ? '#222' : '#fff';
}
</script>

<style scoped>
.factura-box {
    background: #fff;
    border-radius: 12px;
    box-shadow: 0 2px 12px rgba(25,118,210,0.08);
    padding: 18px 12px;
    margin-bottom: 10px;
}
.card {
    font-family: 'Courier New', Courier, monospace;
}

.card-title {
    font-size: 30px;
}

.talonario-list {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    justify-content: center;
    align-items: center;
    margin-top: 10px;
}

.boleta-circle {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: v-bind(colorNumero);
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 22px;
    font-weight: bold;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.12);
    border: 2px solid #fff;
    cursor: pointer;
    transition: transform 0.1s, background 0.2s;
}

.boleta-circle:hover {
    transform: scale(1.08);
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.18);
    background: #1e2a78;
}

.boleta-circle.adquirida {
    background: #3abe4c;
    color: #fff;
    border: 2px solid #fff;
    opacity: 0.7;
    text-decoration: line-through;
}


.modal-content {
    background: #fff;
    border-radius: 18px;
    box-shadow: 0 4px 24px rgba(96, 31, 158, 0.18);
    font-family: 'Courier New', Courier, monospace;
    border: 2px solid #601f9e;
    padding: 10px;
}

.modal-header {
    border-bottom: 1px solid #601f9e;
    background: #f7f7fa;
    border-top-left-radius: 18px;
    border-top-right-radius: 18px;
}

.modal-title {
    font-size: 2rem;
    font-family: 'Courier New', Courier, monospace;
    font-weight: bold;
    letter-spacing: 1px;
}
#modalListado .modal-title {
    color: #fff !important;
}

.modal-body {
    font-size: 1.3rem;
    color: #222;
    background: #fff;
    padding: 18px 10px;
}

.modal-footer {
    border-top: 1px solid #601f9e;
    background: #f7f7fa;
    border-bottom-left-radius: 18px;
    border-bottom-right-radius: 18px;
}

.btn-primary.w-100 {
    background: #601f9e;
    border: none;
    font-size: 1.2rem;
    font-family: 'Courier New', Courier, monospace;
    font-weight: bold;
    padding: 12px 0;
    border-radius: 10px;
    transition: background 0.2s;
}

.btn-primary.w-100:disabled {
    background: #3abe4c;
    opacity: 0.7;
}

.form-label {
    font-family: 'Courier New', Courier, monospace;
    font-size: 1.1rem;
    color: #601f9e;
    font-weight: bold;
}

.form-control {
    font-family: 'Courier New', Courier, monospace;
    font-size: 1.1rem;
    border-radius: 8px;
    border: 1.5px solid #601f9e;
    margin-bottom: 8px;
}

.form-check-input {
    accent-color: #601f9e;
}


.card-body {
    font-size: 25px
}

.btn {
    font-size: 20px;
    padding: 10px 20px;
}

.bi {
    display: flex;
    justify-content: center;
    align-items: center;
}

.hr {
    border-top: 2px solid black;
    margin: 20px 0;
}

.list-config {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
}
.color-radio {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  font-family: 'Courier New', Courier, monospace;
  font-size: 1.1rem;
  background: #f7f7fa;
  border-radius: 8px;
  padding: 8px 16px;
  margin: 6px 0;
  border: 2px solid #e0e0e0;
}
.color-dot {
  width: 22px;
  height: 22px;
  border-radius: 50%;
  border: 2px solid #fff;
  display: inline-block;
}
@media (max-width:420px){
    .card-title {
        font-size: 25px;
    }
    .card-body {
        font-size: 20px;
    }
}
.table-responsive-custom {
    width: 100%;
    overflow-x: auto;
}

@media (max-width: 600px) {
    .factura-box {
        padding: 8px 2px;
    }
    .table-responsive-custom table {
        font-size: 13px;
        min-width: 400px;
    }
    .modal-content {
        padding: 2px;
    }
}

/* Estilos personalizados para SweetAlert - Modal de ganador personalizado */
:deep(.custom-swal-popup) {
    border: 2px solid #601f9e !important;
    border-radius: 18px !important;
    box-shadow: 0 4px 24px rgba(96, 31, 158, 0.18) !important;
    font-family: 'Courier New', Courier, monospace !important;
    padding: 0 !important;
}

:deep(.custom-swal-popup .swal2-header) {
    background: #601f9e !important;
    color: #fff !important;
    border-top-left-radius: 16px !important;
    border-top-right-radius: 16px !important;
    padding: 20px !important;
    margin: 0 !important;
}

:deep(.custom-swal-title) {
    color: #fff !important;
    font-family: 'Courier New', Courier, monospace !important;
    font-weight: bold !important;
    font-size: 2rem !important;
    margin: 0 !important;
    background: #601f9e !important;
    padding: 20px !important;
    border-top-left-radius: 16px !important;
    border-top-right-radius: 16px !important;
}

:deep(.custom-swal-content) {
    font-family: 'Courier New', Courier, monospace !important;
    color: #222 !important;
    background: #fff !important;
    padding: 18px 20px !important;
    margin: 0 !important;
}

:deep(.swal2-actions) {
    background: #f7f7fa !important;
    border-bottom-left-radius: 16px !important;
    border-bottom-right-radius: 16px !important;
    border-top: 1px solid #601f9e !important;
    padding: 15px 20px !important;
    margin: 0 !important;
}

:deep(.swal2-confirm) {
    background: #601f9e !important;
    font-family: 'Courier New', Courier, monospace !important;
    font-weight: bold !important;
    font-size: 1.2rem !important;
    padding: 12px 24px !important;
    border-radius: 10px !important;
    border: none !important;
}

:deep(.swal2-cancel) {
    font-family: 'Courier New', Courier, monospace !important;
    font-size: 1.2rem !important;
    padding: 12px 24px !important;
    border-radius: 10px !important;
}
</style>