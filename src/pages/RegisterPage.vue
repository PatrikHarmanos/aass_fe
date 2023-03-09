<template>
  <q-page class="page row items-center justify-center">
    <div class="main_div items-center justify-center">
      <div>
        <h3 class="heading column items-center">{{ $t('signUp') }}</h3>
      </div>
      <div class="data items-center justify-center">
        <q-input
          class="label"
          outlined
          v-model="name"
          type="text"
          :label="$t('name')"
          :dense="dense"
        />
        <div class="bad_data" v-if="verify_name === false">
          *Enter your name
        </div>
        <q-input
          class="label"
          outlined
          v-model="surname"
          type="text"
          :label="$t('surname')"
          :dense="dense"
        />
        <div class="bad_data" v-if="verify_surname === false">
          *Enter your surname
        </div>
        <q-input
          class="label"
          outlined
          v-model="email"
          type="email"
          :label="$t('labelEmail')"
          :dense="dense"
        />
        <div class="bad_data" v-if="verify_email === false">
          *Enter your email
        </div>
        <q-input
          class="label"
          outlined
          v-model="password"
          type="password"
          :label="$t('password')"
          :dense="dense"
        />
        <div class="bad_data" v-if="contains_eight_characters === false">
          {{ $t('eightCharacters') }}
        </div>
        <div class="bad_data" v-if="contains_number === false">
          {{ $t('doesntContainNumber') }}
        </div>
        <div class="bad_data" v-if="contains_uppercase === false">
          {{ $t('doesntContainCapitalLetter') }}
        </div>
        <div class="bad_data" v-if="contains_lovercase === false">
          {{ $t('doesntContainSmallLetter') }}
        </div>
        <div class="bad_data" v-if="contains_special_character === false">
          {{ $t('doesntContainSpecialChar') }}
        </div>
        <q-input
          class="label"
          outlined
          v-model="password_verify"
          type="password"
          :label="$t('passwordRepeat')"
          :dense="dense"
        />
        <div class="bad_data" v-if="verify_password === false">
          *Passwords are not identical
        </div>
      </div>
      <div class="column items-center justify-center">
        <q-btn class="button" @click="checkData">{{ $t('signIn') }}</q-btn>
      </div>

      <div class="row justify-center">
        <p class="q-mr-sm">{{ $t('alreadyHaveAccount') }}</p>
        <a href="/#/login">{{ $t('signIn') }}</a>
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router';
import { ref } from 'vue';
import { Notify } from 'quasar';
import { useAuthStore } from '../stores/auth-store';
import auth from 'src/boot/auth';
const authStore = useAuthStore();

const router = useRouter();
const dense = ref(false);
const isPwd = ref(true);

const name = ref('');
const surname = ref('');
const email = ref('');
const password = ref('');
const password_verify = ref('');

const verify_name = ref();
const verify_surname = ref();
const verify_email = ref();
const password_ok = ref();

const verify_password = ref();
const contains_eight_characters = ref();
const contains_number = ref();
const contains_uppercase = ref();
const contains_lovercase = ref();
const contains_special_character = ref();

function emptyString(str: string) {
  if (str === '') {
    return false;
  } else return true;
}

function containsSpecialChars(str: string) {
  const specialChars = /[ `!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?~]/;
  return specialChars.test(str);
}

function isEmail(str: string) {
  const at = /[ `@]/;
  const dot = /[ `.]/;
  if (at.test(str) && dot.test(str) && emptyString(str)) {
    return true;
  } else return false;
}

function checkPassword() {
  if (password.value === password_verify.value) {
    verify_password.value = true;
  } else verify_password.value = false;

  if (password.value.length >= 8) {
    contains_eight_characters.value = true;
  } else contains_eight_characters.value = false;

  contains_number.value = /\d/.test(password.value);
  if (contains_number.value != true) {
    contains_number.value = false;
  }
  contains_uppercase.value = /[A-Z]/.test(password.value);
  if (contains_uppercase.value != true) {
    contains_uppercase.value = false;
  }
  contains_lovercase.value = /[a-z]/.test(password.value);
  if (contains_lovercase.value != true) {
    contains_lovercase.value = false;
  }
  contains_special_character.value = containsSpecialChars(password.value);
  if (contains_special_character.value != true) {
    contains_special_character.value = false;
  }

  if (
    contains_eight_characters.value === true &&
    contains_special_character.value === true &&
    contains_uppercase.value === true &&
    contains_lovercase.value === true &&
    contains_number.value === true &&
    verify_password.value === true
  ) {
    return true;
  } else return false;
}

function checkData() {
  // verify_name.value = emptyString(name.value);
  // verify_surname.value = emptyString(surname.value);
  // verify_email.value = isEmail(email.value);
  // password_ok.value = checkPassword();

  let data = {
    first_name: name.value,
    last_name: surname.value,
    email: email.value,
    password: password.value,
    password_confirmation: password_verify.value,
  };
  authStore.register(JSON.stringify(data)).then((res) => {
    console.log(res);
    Notify.create({
      message: 'Účet bol úspešne vytvorený.',
      position: 'top-right',
      type: 'positive',
    });
  });
  // router.push('/login');
}
</script>

<style scoped lang="scss">
.page {
  background-color: #f0f6fb;
}
.heading {
  margin-bottom: 20px;
  color: #064789;
}
.main_div {
  width: 40%;
  border-radius: 25px;
  background-color: white;
  box-shadow: 8px 8px 18px 10px rgba(216, 216, 216, 0.916);
  padding: 50px;
  padding-top: 0px;
  margin-top: 2%;
  margin-bottom: 2%;
}
.label {
  margin-top: 25px;
}
.bad_data {
  color: red;
}
.button {
  color: #ebf2fa;
  background-color: #064789;
  margin-top: 20px;
  margin-bottom: 0px;
}
</style>
