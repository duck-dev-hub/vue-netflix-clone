<template>
  <div class="tile bg">
    <div class="tile__container">
      <h1 class="tile__title">
        Recover Password
      </h1>
      <form @submit.prevent="onSignup">
        <div class="form__field">
          <div :class="[{'input--error': errors.password.length !== 0}, 'input__wrapper']">
            <input
              :class="[{'input--filled': password}, 'input']"
              name="password"
              id="password"
              v-model="password"
              type="password"
              required
              placeholder="password"
            >
            <label class="input__placeholder" for="password">
              Password
            </label>
          </div>
          <ul class="form__error-list">
            <li class="form__error-item" v-for="(error, index) in errors.password" :key="index">
              {{error}}
            </li>
          </ul>
          <ul class="form__required-list">
            <li :class="[{'form__required-item--done': password.length >= 8}, 'form__required-item']">
              <span class="form__required-text">
                At least 8 characters long
              </span>
            </li>
            <li :class="[{'form__required-item--done': password !== password.toLowerCase()}, 'form__required-item']">
              <span class="form__required-text">
                One uppercase character
              </span>
            </li>
            <li :class="[{'form__required-item--done': password !== password.toUpperCase()}, 'form__required-item']">
              <span class="form__required-text">
                One lowercase character
              </span>
            </li>
            <li :class="[{'form__required-item--done': isLatin(password)}, 'form__required-item']">
              <span class="form__required-text">
                Latin characters only
              </span>
            </li>
          </ul>
        </div>
        <div class="form__field">
          <div :class="[{'input--error': errors.password.length !== 0}, 'input__wrapper']">
            <input
              :class="[{'input--filled': confirmPassword}, 'input']"
              name="confirm-password"
              id="confirm-password"
              v-model="confirmPassword"
              type="password"
              required
              placeholder="Confirm Password"
            >
            <label class="input__placeholder" for="confirm-password">
              Confirm Password
            </label>
          </div>
          <ul class="form__error-list">
            <li class="form__error-item" v-for="(error, index) in errors.confirmPassword" :key="index">
              {{error}}
            </li>
          </ul>
        </div>
        <button
          type="submit"
          class="btn btn--red form__btn"
          :disabled="loading"
        >
          Change Password
        </button>
      </form>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        password: "",
        confirmPassword: "",
        errors: {
          password: [],
          confirmPassword: [],
        },
      };
    },
    computed: {
      user() {
        return this.$store.getters.user;
      },
      error() {
        return this.$store.getters.error;
      },
      loading() {
        return this.$store.getters.loading;
      }
    },
    watch: {
      user(value) {
        if (value !== null && value !== undefined) {
          this.$router.push("/profile");
        }
      }
    },
    methods: {
      isFormValid() {
        const { password, confirmPassword } = this;
        this.errors = {
          password: [],
          confirmPassword: [],
        };
        if (password !== confirmPassword) {
          this.errors.confirmPassword.push('Passwords do not match.')
        }
        if (password.length < 6) {
          this.errors.password.push('Password should be at least 6 characters.')
        }
        if (password === password.toLowerCase() || password === password.toUpperCase()) {
          this.errors.password.push('Contains at least one uppercase and lowercase characters.')
        }
        if (!this.isLatin(password)) {
          this.errors.password.push('Latin characters and numbers only.')
        }
        return Object.values(this.errors).every(field => field.length === 0);
      },
      isLatin(password) {
        let ifLatin =  /^[a-zA-z0-9_]+$/g;
        return ifLatin.test(password);
      },
      onSignup() {
        if (!this.isFormValid()) return null;
        this.$store.dispatch("recoverPassword", {
          newPassword: this.password,
          code: this.$route.query.oobCode,
        });
      },
      onDismissed() {
        this.$store.dispatch("clearError");
      }
    }
  };
</script>

<style lang="scss">

</style>
