,<template>
  <div
    :class="{
      'bg-yellow-800': notification.error === 'Unauthorized',
      'bg-red-800': notification.error !== 'Unauthorized',
      'text-yellow-100': notification.error === 'Unauthorized',
      'text-red-100': notification.error !== 'Unauthorized'
    }"
    class="p-2 leading-none lg:rounded-full flex lg:inline-flex items-center"
    role="alert"
  >
    <span
      class="flex rounded-full uppercase px-2 py-1 text-xs font-bold mr-3"
      :class="{
        'bg-yellow-500': notification.error === 'Unauthorized',
        'bg-red-500': notification.error === 'Unauthorized'
      }"
      >{{ notification.statusCode }}</span
    >
    <span class="font-semibold mr-2 text-left flex-auto">{{
      notification.message
    }}</span>
  </div>
</template>

<script>
export default {
  name: 'AlertComponent',
  props: ['notification']
}
</script>

<style></style>
