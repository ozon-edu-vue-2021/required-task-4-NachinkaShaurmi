<template>
  <form autocomplete="off" class="form" @submit.prevent="formSubmit">
    <h2>Личные данные</h2>

    <div class="row">
      <div class="column">
        <label for="name"> Имя </label>
        <input id="name" />
      </div>

      <div class="column">
        <label for="surname"> Фамилия </label>
        <input id="surname" />
      </div>

      <div class="column">
        <label for="patronymic"> Отчество </label>
        <input id="patronymic" />
      </div>
    </div>

    <div class="column">
      <label for="age"> Дата рождения </label>
      <input id="age" />
    </div>

    <div class="column">
      <label for="mail"> E-mail </label>
      <input id="mail" />
    </div>

    <div class="column">
      <label for="gender"> Пол </label>
      <div class="row">
        <div class="row">
          <input
            type="radio"
            id="gender"
            value="man"
            v-model="user.gender"
          /><span>Мужской</span>
        </div>
        <div class="row">
          <input
            type="radio"
            id="gender"
            value="woman"
            v-model="user.gender"
          /><span>Женский</span>
        </div>
      </div>
    </div>

    <h2>Паспортные данные</h2>

    <div class="column">
      <label for="citizenship"> Гражданство </label>
      <div class="citizenship-block" v-click-outside="hideDropdown">
        <input id="citizenship" @focus="isDropdownOpen = true" />
        <div v-if="isDropdownOpen" class="skill-selector___dropdown">
          <ul>
            <li v-for="ctz in citizenshipList" :key="ctz.uid">
              {{ ctz.nationality }}
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="column">
        <label for="passportSeries"> Серия паспорта </label>
        <input id="passportSeries" />
      </div>

      <div class="column">
        <label for="passportNumber"> Номер паспорта </label>
        <input id="passportNumber" />
      </div>

      <div class="column">
        <label for="passportDate"> Дата выдачи </label>
        <input id="passportDate" />
      </div>
    </div>

    <div class="column">
      <label for="changeName"> Меняли ли фамилию или имя </label>
      <div class="row">
        <div class="row">
          <input
            type="radio"
            id="changeName"
            value="true"
            v-model="user.changeName"
          /><span>Да</span>
        </div>
        <div class="row">
          <input
            type="radio"
            id="changeName"
            value="false"
            v-model="user.changeName"
          /><span>Нет</span>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="column">
        <label for="newName"> Имя </label>
        <input id="newName" />
      </div>

      <div class="column">
        <label for="newSurname"> Фамилия </label>
        <input id="newSurname" />
      </div>
    </div>

    <button>Отправить</button>
  </form>
</template>

<script>
import ClickOutside from "vue-click-outside";
import citizenships from "../assets/data/citizenships.json";

export default {
  directives: {
    ClickOutside,
  },
  data() {
    return {
      isDropdownOpen: false,
      citizenshipList: citizenships,
      user: {
        name: "",
        surname: "",
        patronymic: "",
        age: "",
        mail: "",
        gender: "",
        changeName: "",
      },
    };
  },
  methods: {
    hideDropdown() {
      this.isDropdownOpen = false;
    },
  },
};
</script>

<style scoped>
.form {
  border-radius: 6px;
  border: 1px solid #ccc;
  background: #fff;
  max-width: 1200px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin: 20px auto;
  padding: 10px;
}

.row {
  display: flex;
  align-items: center;
  gap: 20px;
  flex-wrap: wrap;
}

.column {
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-left: 20px;
}

.citizenship-block {
  display: flex;
  flex-direction: column;
}

button {
  border-radius: 6px;
  border: 1px solid #ccc;
  color: white;
  padding: 10px 20px;
  max-width: 160px;
  background-color: blueviolet;
  cursor: pointer;
  transition: 0.5s;
  align-self: end;
}

button:hover {
  background-color: blue;
}
</style>
