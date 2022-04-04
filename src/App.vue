<script>
import Button from './components/UI/Button.vue';
export default {
  components: {
    Button,
  },
  data: () => ({
    user: {
      fullName: '',
      avatar: '',
      age: '',
      employment: '',
      id: '',
    },
    beer: {},
    friends: [],
    showBeer: false,
    block: false,
  }),

  methods: {
    activeBeer() {
      this.showBeer = true;
    },
    async loadBeer() {
      const dataBeer = await fetch(
        'https://random-data-api.com/api/beer/random_beer'
      ).then(response => response.json());
      this.beer = dataBeer;
    },
    async loadUser() {
      const res = await fetch(
        'https://random-data-api.com/api/users/random_user'
      ).then(response => response.json());

      this.user.fullName = res.first_name + ' ' + res.last_name;
      this.user.avatar = res.avatar;

      const yearOfBirth = new Date(res.date_of_birth).getFullYear();
      const currentYear = new Date().getFullYear();
      this.user.age = currentYear - yearOfBirth;

      this.user.employment = res.employment.title;
      this.user.id = res.id;
    },
    loadFriend() {
      this.block = false;
      this.loadUser();
      this.loadBeer();
    },
    loadOtherBeer() {
      this.loadBeer();
    },
    addFriend() {
      this.friends.push({
        name: `${this.user.fullName}`,
        beer: `${this.beer.name}`,
        id: `${this.user.id}`,
      });
      this.block = true;
    },
  },

  async created() {
    this.loadUser();
    this.loadBeer();
  },
};
</script>

<template>
  <h1 class="px-4 mt-12 text-white text-center text-4xl font-serif">
    Сервис самого лучшего пива!
  </h1>
  <div
    class="mx-auto mt-12 px-4 font-serif text-lg font-semibold"
    style="max-width: 870px"
  >
    <div class="md:flex">
      <div class="bg-slate-50 rounded-lg w-auto md:w-full m-2">
        <h2 class="text-center pt-2 text-3xl">Это вы :)</h2>
        <div class="p-4 text-center">
          <img
            class="w-44 mx-auto my-2 h-auto rounded-full border-black border-2"
            :src="user.avatar"
            alt=""
          />
          <div>
            <p class="mt-6 flex justify-between">
              Полное имя: <span>{{ user.fullName }}</span>
            </p>
            <p class="flex justify-between">
              Возраст: <span>{{ user.age }}</span>
            </p>
            <h2 class="flex justify-between">
              Профессия: <span>{{ user.employment }}</span>
            </h2>
            <hr />
            <h2 class="mt-2 text-black text-center text-3xl">А теперь друг!</h2>
            <Button @click="loadFriend" class="mt-3">Загрузить</Button>
          </div>
        </div>
      </div>
      <div class="bg-slate-50 rounded-lg md:w-full m-2">
        <h2 v-if="!showBeer" class="pt-2 text-black text-center text-3xl">
          Нажми, и ты увидишь самое лучшее пиво!
        </h2>
        <h2 v-else class="pt-2 text-black text-center text-3xl">
          Не понравилось? Попробуй еще раз!
        </h2>
        <div class="flex justify-center mt-3">
          <Button v-if="!showBeer" @click="activeBeer" class="mb-4"
            >Показать</Button
          >
          <Button v-else @click="loadOtherBeer">Поменять</Button>
        </div>
        <div class="p-4 text-center" v-show="showBeer">
          <p class="flex justify-between">
            Название: <span>{{ beer.name }}</span>
          </p>
          <p class="flex justify-between">
            Бренд: <span> {{ beer.brand }}</span>
          </p>
          <p class="flex justify-between">
            Процент алкоголя: <span>{{ beer.alcohol }}</span>
          </p>
          <p class="flex justify-between">
            Содержание сахара: <span>{{ beer.blg }}</span>
          </p>
          <p class="flex justify-between">
            Уровень горечи: <span>{{ beer.ibu }}</span>
          </p>
          <p class="flex justify-between">
            Солод: <span>{{ beer.malts }}</span>
          </p>
          <hr />
          <Button @click="addFriend" :disabled="block" class="mt-4"
            >Выбрать</Button
          >
        </div>
      </div>
    </div>
    <div class="mt-4 text-white w-full">
      <h1 v-show="friends.length" class="text-center">Ваш выбор!</h1>
      <div
        v-for="friend in friends"
        :key="friend.id"
        class="bg-slate-50 rounded-lg text-black m-2 p-4 text-center"
      >
        <p>{{ friend.name }} - {{ friend.beer }}</p>
      </div>
      <div v-show="friends.length" class="flex justify-center mb-2">
        <Button @click="friends.length = 0">Сбросить</Button>
      </div>
    </div>
  </div>
</template>

<style>
body {
  background: #4b637c;
}
</style>
