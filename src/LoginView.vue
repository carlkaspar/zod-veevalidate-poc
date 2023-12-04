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

const validationSchema = z.object({
  email: emailSchema(),
  password: passwordSchema(),
})

const { handleSubmit } = useForm({
  initialValues: { email: '', password: '' },
  validationSchema: toTypedSchema(validationSchema),
})
const { value: email, errorMessage: emailError } = useField<string>('email')
const { value: password, errorMessage: passwordError } = useField<string>('password')

const signIn = handleSubmit(values => {
  // this block only runs when the useForm validationSchema is valid
  // api request here
})

function emailSchema() {
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