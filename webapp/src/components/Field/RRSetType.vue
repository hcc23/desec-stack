<template>
  <v-combobox
    :label="label"
    :disabled="disabled || readonly"
    :error-messages="errorMessages"
    :value="value"
    :items="types"
    :required="required"
    :rules="[v => !required || !!v || 'Required.']"
    @input="input($event)"
  />
</template>

<script>
export default {
  name: 'RRSetType',
  props: {
    disabled: {
      type: Boolean,
      required: false,
    },
    errorMessages: {
      type: [String, Array],
      default: () => [],
    },
    label: {
      type: String,
      required: false,
    },
    readonly: {
      type: Boolean,
      required: false,
    },
    required: {
      type: Boolean,
      default: false,
    },
    value: {
      type: String,
      required: true,
    },
  },
  data: () => ({
    types: [
      'A',
      'AAAA',
      'MX',
      'CNAME',
      'TXT',
      'SPF',
      'CAA',
      'TLSA',
      'OPENPGPKEY',
      'PTR',
      'SRV',
      'DS',
    ],
  }),
  methods: {
    input(event) {
      this.$emit('input', event);
    },
  },
};
</script>

<style>

</style>
