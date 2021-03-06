<template>
  <form v-show="visible">
    <label>First Name</label>
    <input id="fname" type="text" v-model="contact.firstname">
    <label>Last Name</label>
    <input id="lname" type="text" v-model="contact.lastname">
    <div id="email">
      <label>Email</label>
      <input type="text" v-for="(addr, index) in contact.email" v-model="contact.email[index]" :key="index">
      <button id="btnAddEMail" @click.prevent="addEmail">Add email</button>
    </div>
    <div id="phone">
      <label>Phone</label>
      <input type="text" @keyup="purgeNumber(index)" v-for="(number, index) in contact.phoneNumber" v-model="contact.phoneNumber[index]" :key="index">
      <button id="btnAddNumber" @click.prevent="addPhone">Add Phone</button>
    </div>
    <div>
      <button id="btnSubmit" @click.prevent="submit">Submit</button>
      <button id="btnDelete" v-show="!newCon" @click.prevent="removeContact">Delete Contact</button>
      <button id="btnCancel" @click.prevent="cancel">Cancel</button>
    </div>
  </form>
</template>

<script>
/**
 * Event bus for contact
 */
import { ContactBus } from '../bus/ContactBus'

export default {
  name: 'ContactForm',
  props: {
    contact: {
      type: Array,
      required: true
    },
    visible: {
      type: Boolean
    },
    newCon: {
      type: Boolean
    },
    index: {
      type: [Number, String]
    }
  },
  computed: {
    validatedEmail: function (email) {
      /**
       * Returns false if any email
       * in contact.email array is invalid
       * and true otherwise
       */
      return String(email).toLowerCase().match(/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/)
    },
    validatedPhone: function (num) {
      /**
       * Returns false if a phone number
       * in contact.phoneNumber array is
       * invalid and true otherwise
       */
      return (num.value.match(/^\(?([0-9]{3})\)?[-. ]?([0-9]{3})[-. ]?([0-9]{4})$/))
    }
  },
  methods: {
    submit: function () {
      if (this.contact.firstname && this.contact.lastname) {
        if (this.validatedEmail || this.validatedPhone) {
          ContactBus.$emit('addCon', this.contact)
        } else {
          ContactBus.$emit('noAddCon', [])
        }
      } else {
        ContactBus.$emit('noAddCon', [])
      }
    },
    purgeNumber: function (index) {
      this.contact.phoneNumber[index] = this.contact.phoneNumber[index].replace(/\D/g, '')
    },
    cancel: function () {
      ContactBus.$emit('close', true)
    },
    removeContact: function () {
      ContactBus.$emit('delCon', this.index)
    },
    addEmail: function () {
      /**
       * Appends empty string to the end of
       * contact.email array
       */
    },
    addPhone: function () {
      /**
       * Appends empty string to the end of
       * contact.phoneNumber array
       */
    }
  }
}
</script>

<style scoped>
form {
  margin: 30px;
}
input {
  margin: 20px 10px;
}
</style>
