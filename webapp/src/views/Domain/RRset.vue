<template>
  <tr class="rrset">
    <td>
      <v-combobox
        v-model="rrset.type"
        :items="types"
      />
    </td>
    <td>
      <v-text-field
        v-model="rrset.subname"
        placeholder="(optional)"
      />
    </td>
    <td>
      <component
        :is="getRecordComponentName(rrset.type)"
        v-for="(record, index) in rrset.records"
        :key="index"
        :content="record"
        :clearable="rrset.records.length > 1"
        @update:content="$set(rrset.records, index, $event)"
      />
      <code style="white-space: normal">{{ rrset.records }}</code>
    </td>
    <td>
      <v-text-field
        v-model="rrset.ttl"
        type="number"
        :hide-details="!$v.rrset.ttl.$invalid"
        :error="$v.rrset.ttl.$invalid"
        :error-messages="errors"
      />
    </td>
    <td>
      <v-layout
        align-center
        justify-end
      >
        {{ rrset.records.length }}/{{ current() }}
        <v-btn
          color="grey"
          flat
          icon
        >
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn
          class="_delete"
          flat
          icon
          @click.stop="openRRsetDeletionDialog(rrset)"
        >
          <v-icon>mdi-delete</v-icon>
        </v-btn>
        <!--v-checkbox
          :input-value="props.selected"
          primary
          hide-details
          class="shrink"
        ></v-checkbox-->
      </v-layout>
    </td>
  </tr>
</template>

<script>
  import {required, integer, minValue} from 'vuelidate/lib/validators';
  import Record from '@/components/Field/Record.vue';
  import RecordA from '@/components/Field/RecordA.vue';
  import RecordCNAME from '@/components/Field/RecordCNAME.vue';
  import RecordMX from '@/components/Field/RecordMX.vue';
  import RecordSRV from '@/components/Field/RecordSRV.vue';


  const MinTTL = 10;

  export default {
    name: 'RRset',
    components: {
      Record,
      RecordA,
      RecordCNAME,
      RecordMX,
      RecordSRV,
    },
    props: {
      current: {
        type: Function,
        required: true,
      },
      limit: {
        type: Number,
        required: true,
      },
      rrset: {
        type: Object,
        required: true,
      },
    },
    data: () => ({
      errorDict: {
        required: 'This field is required.',
        integer: 'TTL must be an integer.',
        minValue: `The minimum value is ${MinTTL}.`,
      },
      types: ['A', 'AAAA', 'MX', 'CNAME', 'TXT', 'SPF', 'CAA', 'TLSA', 'OPENPGPKEY', 'PTR', 'SRV', 'DS', 'DNSKEY'],
    }),
    computed: {
      errors() {
        return Object.entries(this.errorDict).filter(entry => !this.$v.rrset.ttl[entry[0]]).map(entry => entry[1]);
      },
      left() {
        return this.limit - this.current();
      },
    },
    methods: {
      getRecordComponentName(type) {
        const genericComponentName = 'Record';
        const specificComponentName = genericComponentName + type;
        if (this.types.includes(type) && specificComponentName in this.$options.components) {
          return specificComponentName;
        }
        return genericComponentName;
      },
    },
    validations: {
      rrset: {
        ttl: {
          required,
          integer,
          minValue: minValue(MinTTL),
        },
      },
    },
  };
</script>

<style>
  /* TODO should be scoped, but scoped CSS doesn't work for some reason */
  .rrset td {
    vertical-align: top;
  }
</style>
