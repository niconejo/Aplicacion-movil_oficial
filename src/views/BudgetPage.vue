<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Presupuesto</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <!-- formulario para agregar presupuesto -->
      <ion-item>
        <ion-label position="stacked">Descripción del Presupuesto</ion-label>
        <ion-input
          v-model="newBudget.description"
          placeholder="Descripción del presupuesto"
          @ionInput="validateField('description')"
        ></ion-input>
      </ion-item>
      <p v-if="errors.description" class="error-message">Por favor ingresa una descripción válida.</p>

      <ion-item>
        <ion-label position="stacked">Monto</ion-label>
        <ion-input
          v-model.number="newBudget.amount"
          type="number"
          placeholder="Monto del presupuesto"
          @ionInput="validateField('amount')"
        ></ion-input>
      </ion-item>
      <p v-if="errors.amount" class="error-message">El monto debe ser mayor a 0.</p>

      <ion-button expand="full" @click="addBudget">Agregar Presupuesto</ion-button>

      <!-- lista de presupuestos -->
      <ion-list v-if="budgets.length > 0">
        <ion-item v-for="(budget, index) in budgets" :key="index">
          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ budget.description }}</ion-card-title>
              <ion-card-subtitle>{{ new Date(budget.date).toLocaleString() }}</ion-card-subtitle>
            </ion-card-header>
            <ion-card-content>
              <p><strong>Monto:</strong> {{ formatCurrency(budget.amount) }}</p>
              <ion-button color="danger" expand="block" @click="deleteBudget(index)">Eliminar</ion-button>
            </ion-card-content>
          </ion-card>
        </ion-item>
      </ion-list>

      <!-- mensaje cuando no existe presupuesto -->
      <ion-item v-else>
        <ion-label>No hay presupuestos registrados.</ion-label>
      </ion-item>
    </ion-content>
  </ion-page>
</template>

<script setup>
import { ref } from 'vue';
import { state } from '../state'; 

import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonItem,
  IonLabel,
  IonInput,
  IonButton,
  IonList,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardSubtitle,
  IonCardContent
} from '@ionic/vue';

// 'no pude hacerlo xd sadjklasd me estrese, que alguien lo haga porfa, no pude :c kasldjasid' dr

// 'ya lo hice mano igual revisa si' nc

// lista de presupuestos
const budgets = ref(state.budgets || []);

// nuevo presupuesto a agregar
const newBudget = ref({
  description: '',
  amount: 0,
  date: new Date()
});

// errores de validación
const errors = ref({
  description: false,
  amount: false
});

// funcion para agregar presupuesto
const addBudget = () => {
  // limpiar errores previos
  errors.value.description = false;
  errors.value.amount = false;

  // validar campos
  if (!newBudget.value.description.trim()) {
    errors.value.description = true;
  }
  if (newBudget.value.amount <= 0 || isNaN(newBudget.value.amount)) {
    errors.value.amount = true;
  }

  // si hay errores no se puede continuar
  if (errors.value.description || errors.value.amount) {
    return;
  }

  // agregar el nuevo presupuesto a la lista 
  budgets.value.push({
    ...newBudget.value,
    date: new Date() // con esto se pone la fecha de hoy
  });

  // limpiar el formulario
  newBudget.value = {
    description: '',
    amount: 0,
    date: new Date()
  };
};

// funciom para eliminar presupuesto
const deleteBudget = (index) => {
  budgets.value.splice(index, 1);
};

// funcion para formato de moneda
const formatCurrency = (value) => {
  return new Intl.NumberFormat('es-ES', { style: 'currency', currency: 'USD' }).format(value);
};

// funcion para validar campos en tiempo real
const validateField = (field) => {
  if (field === 'description' && newBudget.value.description.trim()) {
    errors.value.description = false;
  }
  if (field === 'amount' && newBudget.value.amount > 0 && !isNaN(newBudget.value.amount)) {
    errors.value.amount = false;
  }
};
</script>




<style scoped>
ion-card {
  margin-bottom: 15px;
}
.error-message {
  color: red;
  font-size: 12px;
  margin-top: 5px;
}
</style>
