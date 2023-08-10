<template>
    <v-row justify="center">
      <v-col cols="12" sm="8" md="6">
        <v-card>
          <v-card-title class="headline">
            Add New Phone
          </v-card-title>
          <v-card-text>
              <form>
                  <v-autocomplete
                  v-model="state.selected_brand"
                  :items="brands"
                  :error-messages="v$.selected_brand.$errors.map(e => e.$message)"
                  label="Brand"
                  required
                  @change="v$.selected_brand.$touch"
                  @blur="v$.selected_brand.$touch">
                  </v-autocomplete>
  
  <v-text-field
  v-model="state.phone_model"
        label="Model"
        required
        @input="v$.phone_model.$touch"
        @blur="v$.phone_model.$touch"
      ></v-text-field>
  
  <v-select
  v-model="state.selected_option"
  :items="options"
  :error-messages="v$.selected_option.$errors.map(e => e.$message)"
        label="Option"
        required
        @change="v$.selected_option.$touch"
        @blur="v$.selected_option.$touch"
      ></v-select>
  
      <v-text-field
      v-model="state.product_code"
      :error-messages="v$.product_code.$errors.map(e => e.$message)"
        label="Product code"
        required
        @input="v$.product_code.$touch"
        @blur="v$.product_code.$touch"
      ></v-text-field>
  
      <v-select
            v-model="state.selected_currency"
            :items="currencies"
            :error-messages="v$.selected_currency.$errors.map(e => e.$message)"
            label="Currency"
            required
            @change="v$.selected_currency.$touch"
            @blur="v$.selected_currency.$touch"
          ></v-select>

      <v-text-field
        v-model="state.cost_price"
        :error-messages="v$.cost_price.$errors.map(e => e.$message)"
        label="Cost price"
        type="number"
        required
        @input="v$.cost_price.$touch"
        @blur="v$.cost_price.$touch"
      ></v-text-field>  
  
      <v-text-field
        v-model="state.commission"
        :error-messages="v$.commission.$errors.map(e => e.$message)"
        label="Seller commission"
        type="number"
        required
        @input="v$.commission.$touch"
        @blur="v$.commission.$touch"
      ></v-text-field>
      
      <v-text-field
        v-model="state.sale_price"
        :error-messages="v$.sale_price.$errors.map(e => e.$message)"
        label="Sale price"
        type="number"
        required
        @input="v$.sale_price.$touch"
        @blur="v$.sale_price.$touch"
      ></v-text-field>   
  
      <v-file-input 
        v-model="state.photo"
        :error-messages="v$.photo.$errors.map(e => e.$message)"
        label="Upload a photo" 
        accept="image/*" 
        required
        @change="previewImage()"
        @input="v$.photo.$touch"
        @blur="v$.photo.$touch"
        :resetPhotoField="resetPhotoField"
        ></v-file-input>
      <template v-if="state.preview">
          <img :src="state.preview" class="img-fluid" />
          <v-text-field
          v-model="state.photo_url"
          readonly
        label="Photo url"
      ></v-text-field>   
      </template>
      <v-checkbox
        v-model="state.is_recommended"
        label="Recommended product"
        type="checkbox"
      ></v-checkbox>
  
      <v-btn
        @click="submitForm"
        :disabled="v$.$invalid"
        class="me-4"
        type="submit"
      >
        add
      </v-btn>
  
      <v-btn @click="resetForm">
        clear
      </v-btn>
              </form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </template>
  
  <script setup>
  import axios from "axios"
  import { useToast } from "vue-toastification";
  import { useVuelidate } from '@vuelidate/core'
  import { required, minLength } from '@vuelidate/validators'
  import { reactive, computed } from "vue"
  import brands from "~/js/brands.js"
  import options from "~/js/options.js"
  import currencies from "~/js/currencies.js"
    const initialState = [
      {
        selected_brand: '',
      selected_option: '',
      product_code: '',
      cost_price: 0,
      commission: 0,
      sale_price: 0,
      photo: '',
      preview: '',
      image: '',
      phone_model: '',
      photo_url: '',
      is_recommended: false,
      selected_currency: ''
      }
    ]
  const toast = useToast();

  const resetPhotoField = computed(() => {
    // Note: we are using destructuring assignment syntax here.
    if (state.photo == '')
      return state.photo_url, state.image, state.preview = ''
  })

  const state = reactive({
    ...initialState
  })

  const rules = {
      selected_brand: { required },
      selected_option: { required },
      product_code: { required },
      cost_price: { required, minLength: minLength(3) },
      commission: { required, minLength: minLength(2) },
      sale_price: { required, minLength: minLength(3) },
      photo: { required },
      phone_model: { required },
      photo_url: { required },
      selected_currency: { required }
  }

  const v$ = useVuelidate(rules, state)

    const previewImage = () => {
      const input = event.target;
        if (input.files) {
          const reader = new FileReader();
          reader.onload = (e) => {
            state.preview = e.target.result;
          }
          state.image = input.files[0];
          reader.readAsDataURL(input.files[0]);
          const url = URL.createObjectURL(state.image)
          state.photo_url = url
    }
  }

    const submitForm = () => {
      axios.post("http://localhost:3000/data", {
                  brand: state.selected_brand,
                  model: state.phone_model,
                  option: state.selected_option,
                  serial: state.product_code,
                  cost_price: parseFloat(state.cost_price),
                  commission: parseFloat(state.commission),
                  sale_price: parseFloat(state.sale_price),
                  currency: state.selected_currency,
                  cover_img: state.photo_url,
                  recommended: state.is_recommended
                })
                .then(response => {
                  toast.success("Phone successfully added!", { timeout: 2000 })
                })
                .catch(() => {
                  toast.success("Something went wrong! Please try again.", { timeout: 2000 })
                })
                resetForm()
    }

    const resetForm = () => {
      v$.value.$reset()
      
      for (const [key, value] of Object.entries(initialState[0])) {
        state[key] = value
      }
    }
  </script>
  
  <style>
  
  </style>