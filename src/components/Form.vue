<template>
  <form autocomplete="off" class="form" @submit.prevent="formSubmit">
    <h2>Личные данные</h2>

    <div class="row">
      <div class="column">
        <label for="name"> Имя </label>
        <input id="name" v-model="user.name" ref="name" />
      </div>

      <div class="column">
        <label for="surname"> Фамилия </label>
        <input id="surname" v-model="user.surname" ref="surname" />
      </div>

      <div class="column">
        <label for="patronymic"> Отчество </label>
        <input id="patronymic" v-model="user.patronymic" ref="patronymic" />
      </div>
    </div>

    <div class="column">
      <label for="age"> Дата рождения </label>
      <input id="age" v-model="user.age" placeholder="дд.мм.гггг" ref="age" />
    </div>

    <div class="column">
      <label for="mail"> E-mail </label>
      <input id="mail" v-model="user.mail" type="email" ref="mail" />
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
        <input id="citizenship" v-model="ctzSearch" @focus="citizenshipFocus" />
        <div v-if="isDropdownOpen">
          <ul>
            <li
              v-for="ctz in filtredCtzList"
              :key="ctz.uid"
              @click="ctzClick(ctz)"
            >
              {{ ctz.nationality }}
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div class="row" v-if="user.citizenship === 'Russia'">
      <div class="column">
        <label for="passportSeries"> Серия паспорта </label>
        <input
          id="passportSeries"
          v-model="user.passport.series"
          ref="passportSeries"
        />
      </div>

      <div class="column">
        <label for="passportNumber"> Номер паспорта </label>
        <input
          id="passportNumber"
          v-model="user.passport.number"
          ref="passportNumberRus"
        />
      </div>

      <div class="column">
        <label for="passportDate"> Дата выдачи </label>
        <input
          id="passportDate"
          v-model="user.passport.date"
          placeholder="дд.мм.гггг"
        />
      </div>
    </div>

    <template v-if="user.citizenship && user.citizenship !== 'Russia'">
      <div class="row">
        <div class="column">
          <label for="latSurname"> Фамилия на латинице </label>
          <input id="latSurname" v-model="user.latSurname" ref="latSurname" />
        </div>

        <div class="column">
          <label for="latName"> Имя на латинице </label>
          <input id="latName" v-model="user.latName" ref="latName" />
        </div>
      </div>

      <div class="row">
        <div class="column">
          <label for="passportNumber"> Номер паспорта </label>
          <input
            id="passportNumber"
            v-model="user.passport.number"
            ref="passportNumberNotRus"
          />
        </div>

        <div class="column">
          <label for="passportCountry"> Страна выдачи </label>
          <select id="passportCountry" v-model="user.passport.country">
            <option v-for="ctz in citizenshipList" :key="ctz.uid">
              {{ ctz.nationality }}
            </option>
          </select>
        </div>

        <div class="column">
          <label for="passportType"> Тип паспорта </label>
          <select id="passportType" v-model="user.passport.type">
            <option v-for="type in passportTypes" :key="type.id">
              {{ type.type }}
            </option>
          </select>
        </div>
      </div>
    </template>

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

    <div class="change-block" v-if="user.changeName === 'true'">
      <div class="row">
        <div class="column">
          <label for="prevName"> Предыдущее имя </label>
          <input id="prevName" v-model="user.prevName" ref="prevName" />
        </div>

        <div class="column">
          <label for="prevSurname"> Предыдущяя фамилия </label>
          <input
            id="prevSurname"
            v-model="user.prevSurname"
            ref="prevSurname"
          />
        </div>
      </div>
    </div>

    <button>Отправить</button>
  </form>
</template>

<script>
import ClickOutside from "vue-click-outside";
import { DateTime } from "luxon";
import citizenships from "../assets/data/citizenships.json";
import passportTypes from "../assets/data/passport-types.json";
import debounce from "../helpers/debounce";

export default {
  directives: {
    ClickOutside,
  },
  data() {
    return {
      isDropdownOpen: false,
      citizenshipList: citizenships,
      passportTypes: passportTypes,
      ctzSearch: "",
      user: {
        name: "",
        surname: "",
        patronymic: "",
        age: "",
        mail: "",
        gender: "",
        citizenship: "",
        passport: {
          series: "",
          number: "",
          date: "",
          type: "",
          country: "",
        },
        latName: "",
        latSurname: "",
        changeName: "",
        prevName: "",
        prevSurname: "",
      },
      filtredCtzList: citizenships,
      debouncedFilterList: null,
      validation: null,
    };
  },
  methods: {
    hideDropdown() {
      this.ctzSearch = this.user.citizenship || "";
      this.isDropdownOpen = false;
    },
    citizenshipFocus() {
      this.ctzSearch = "";
      this.filtredCtzList = citizenships;
      this.isDropdownOpen = true;
    },
    formSubmit() {
      const EMAIL_REG_EXP = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      const CYRILLIC = /^[а-яА-я]+$/;
      const LAT = /^[a-zA-Z]+$/;
      const PASS_SERIES = /^\d{4}$/;
      const PASS_NUMBER_RUS = /^\d{6}$/;
      const PASS_NUMBER_NOT_RUS = /.*/;

      this.validation(CYRILLIC, this.user.name, "name");
      this.validation(CYRILLIC, this.user.surname, "surname");
      this.validation(CYRILLIC, this.user.patronymic, "patronymic");
      this.validation(EMAIL_REG_EXP, this.user.mail, "mail");

      if (this.user.citizenship === "Russia") {
        this.validation(
          PASS_SERIES,
          this.user.passport.series,
          "passportSeries"
        );
        this.validation(
          PASS_NUMBER_RUS,
          this.user.passport.number,
          "passportNumberRus"
        );
      }

      if (this.user.citizenship && this.user.citizenship !== "Russia") {
        this.validation(LAT, this.user.latName, "latName");
        this.validation(LAT, this.user.latSurname, "latSurname");
        this.validation(
          PASS_NUMBER_NOT_RUS,
          this.user.passport.number,
          "passportNumberNotRus"
        );
      }
      if (this.user.changeName === "true") {
        this.validation(CYRILLIC, this.user.prevName, "prevName");
      }
      if (this.user.changeName === "true") {
        this.validation(CYRILLIC, this.user.prevSurname, "prevSurname");
      }

      const [day, month, year] = this.user.age.split(".");
      const notValid =
        Number.isNaN(+day) || Number.isNaN(+month) || Number.isNaN(+year);
      if (notValid) return (this.$refs["age"].classList.value = "invalid");

      const dateFromForm = DateTime.fromObject({
        year: year,
        month: month,
        day: day,
      });
      const currentDate = DateTime.now();

      dateFromForm.isValid && currentDate > dateFromForm
        ? (this.$refs["age"].classList.value = "")
        : (this.$refs["age"].classList.value = "invalid");
    },
    ctzClick(el) {
      this.user.citizenship = el.nationality;
      this.user.passport.number = "";
      this.ctzSearch = el.nationality;
      this.isDropdownOpen = false;
    },
    filterList(newValue) {
      this.filtredCtzList = citizenships.filter((el) =>
        el.nationality.toLowerCase().includes(newValue.toLowerCase())
      );
    },
  },
  watch: {
    ctzSearch(newValue) {
      this.debouncedFilterList(newValue);
    },
  },
  created() {
    this.debouncedFilterList = debounce(this.filterList, 1000);
    this.validation = (regExp, value, refName) => {
      regExp.test(value)
        ? (this.$refs[refName].classList.value = "")
        : (this.$refs[refName].classList.value = "invalid");
    };
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

.invalid {
  background-color: rgba(255, 0, 0, 0.3);
}
</style>
