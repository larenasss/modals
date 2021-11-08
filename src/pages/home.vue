<template>
  <div class="btn-wrapper">
    <button class="btn btn_primary" @click="this.isShowModalFirst = true">Show first modal</button>
    <button class="btn btn_primary" @click="openModalSecond">Show modal whith form</button>
  </div>

  <Modal
    title='First title'
    :isShowProp="isShowModalFirst"
    @close="this.isShowModalFirst = false">
    <template v-slot:body>
      <div>
        <button
          class="btn btn_primary"
          @click="this.isShowModalFirst = false">
            Well Done!
        </button>
      </div>
    </template>
  </Modal>

  <Modal
    title='Second title'
    :isShowProp="modalSecond.isShow"
    @close="modalSecondClose">
    <template v-slot:body>
      <form class="form" @submit.prevent="submitSecondModal">
        <div class="form__item" :class="{ errorInput: v$.modalSecond.name.$error }">
          <input
            v-model="v$.modalSecond.name.$model"
            :class="{ error: v$.modalSecond.name.$error }"
            @blur="v$.modalSecond.name.$touch"
            placeholder="Enter name">
          <p class="errorText" v-for="error of v$.modalSecond.name.$errors" :key="error.$uid">
            {{ error.$message }}
          </p>
        </div>
        <div class="form__item" :class="{ errorInput: v$.modalSecond.password.$error }">
          <input
            v-model="v$.modalSecond.password.$model"
            :class="{ error: v$.modalSecond.password.$error }"
            @blur="v$.modalSecond.password.$touch"
            placeholder="Enter password">
          <p class="errorText" v-for="error of v$.modalSecond.password.$errors" :key="error.$uid">
            {{ error.$message }}
          </p>
        </div>
        <div class="form__item" :class="{ errorInput: v$.modalSecond.confirmPassword.$error }">
          <input
            v-model="v$.modalSecond.confirmPassword.$model"
            :class="{ error: v$.modalSecond.confirmPassword.$error }"
            @blur="v$.modalSecond.confirmPassword.$touch"
            placeholder="Repeat password">
          <p class="errorText" v-for="error of v$.modalSecond.confirmPassword.$errors" :key="error.$uid">
            {{ error.$message }}
          </p>
        </div>
        <div class="form__item" :class="{ errorInput: v$.modalSecond.email.$error }">
          <input
            v-model="v$.modalSecond.email.$model"
            :class="{ error: v$.modalSecond.email.$error }"
            @blur="v$.modalSecond.email.$touch"
            placeholder="Enter email">
          <p class="errorText" v-for="error of v$.modalSecond.email.$errors" :key="error.$uid">
            {{ error.$message }}
          </p>
        </div>
        <button type="submit" class="btn btn_primary">Submit</button>
      </form>
    </template>
  </Modal>
</template>

<script>
  import useVuelidate from '@vuelidate/core'
  import { required, email, sameAs } from '@vuelidate/validators'

  import Modal from '@/components/Modals/Modal.vue';
  export default {
    components: {
      Modal,
    },
    setup () {
      return { v$: useVuelidate() }
    },
    data() {
      return {
        isShowModalFirst: false,
        modalSecond: {
          isShow: false,
          name: '',
          email: '',
          password: '',
          confirmPassword: ''
        },
      }
    },
    validations () {
      return {
        modalSecond: {
          name: {
            required,
          },
          email: {
            required,
            email,
          },
          password: {
            required,
          },
          confirmPassword: {
            required,
            confirmPassword: sameAs(this.modalSecond.password)
          },
        }
      }
    },
    methods: {
      openModalSecond() {
        this.v$.$reset();
        this.modalSecond.isShow = true;
      },
      modalSecondClose() {
        this.resetSecondModalForm();
      },
      resetSecondModalForm() {
        this.modalSecond.name = '';
        this.modalSecond.email = '';
        this.modalSecond.password = '',
        this.modalSecond.confirmPassword = '',
        this.modalSecond.isShow = false;
        this.v$.$reset();
      },
      async submitSecondModal() {
        const isFormCorrect = await this.v$.$validate();
        if (!isFormCorrect) return;

        const user = {
          name: this.modalSecond.name,
          email: this.modalSecond.email,
          password: this.modalSecond.password,
          confirmPassword: this.modalSecond.confirmPassword,
        }

        console.log(user);

        this.resetSecondModalForm();
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '../assets/scss/utils/vars.scss';

  .btn-wrapper {
    display: flex;
    justify-content: center;

    & .btn:not(:last-child) {
      margin-right: 1rem;
    }

    @media screen and (max-width: $phoneWidth) {
      flex-direction: column;

      & .btn:not(:last-child) {
        margin-right: 0;
        margin-bottom: 1rem;
      }
    }
  }

  .form {
    &__item {
      margin-bottom: 1rem;

      & .errorText {
        display: none;
        margin-top: 4px;
        font-size: 13px;
        color: red;
      }

      &.errorInput {
        & .errorText {
          display: block;
        }

        & .error {
          border: 1px solid red;
        }
      }
    }
  }
</style>