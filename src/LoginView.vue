<template>
  <form @submit.prevent="signIn" class="flex flex-col w-1/4 mx-auto mt-5 gap-4">
    <input v-model="email" type="email" class="border" :class="emailError ? 'border-red-500' : 'border-gray-500'" />
    <span v-if="emailError" class="text-red-500">{{ emailError }}</span>

    <input v-model="password" type="password" class="border" :class="passwordError ? 'border-red-500' : 'border-gray-500'" />
    <span v-if="passwordError" class="text-red-500">{{ passwordError }}</span>

    <button type="submit" class="bg-blue-500">Log in</button>
  </form>
</template>

<script setup lang="ts">
import { useField, useForm } from 'vee-validate'
import { z } from 'zod'
import { toTypedSchema } from '@vee-validate/zod'

// define a zod object that will be used to validate the form values
// vee-validate uses validationSchema.parse(value) under the hood to
// validate the form on changes. 
const validationSchema = z.object({
  email: emailSchema(),
  password: passwordSchema(),
})

// define the form & its initial values
// toTypedSchema function is used to make zod work with vee-validate
// handleSubmit is a function that accepts a callback as the param, which will be called on successful validation
const { handleSubmit } = useForm({
  initialValues: { email: '', password: '' },
  validationSchema: toTypedSchema(validationSchema),
})
// useField composable to pull out specific values of the form, honestly this feels a little too magic for me but
// it's not too bad, basically it looks for the closest useForm in the context and syncs with that.
const { value: email, errorMessage: emailError } = useField<string>('email')
const { value: password, errorMessage: passwordError } = useField<string>('password')

const signIn = handleSubmit(values => {
  // this block only runs when the useForm validationSchema is valid
  // api request here
})

function emailSchema() {
  // use zod validators & error messages.
  // the messages will be used to throw ZodErrors, which will be caught by the 
  // vee-validate library & useField composable exposes them in errorMessage
  return z
    .string()
    .min(1, 'Email is required')
    .email('Has to be a valid email')
}

function passwordSchema() {
  return z
    .string()
    .min(1, 'Password required')
    .regex(/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/, 'Minimum eight characters, at least one letter and one number')
}
</script>