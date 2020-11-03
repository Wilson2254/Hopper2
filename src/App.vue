<template>
  <div id="app">
    <div class="title">Ролики</div>

    <!-- Вывод ошибки, если запрос был неверн -->
    <div
      :class="{
        error: isError,
        noError: !isError,
      }"
    >
      Данные не найдены
    </div>

    <!-- Страницы и фильтр -->
    <div class="header">
      <div class="page">Список {{ page + 1 }} / {{ lastPage }}</div>
      <input
        type="text"
        class="find"
        v-model="search"
        placeholder="Фильтр по имени"
      />
    </div>

    <!-- Список роликов -->
    <div class="list">
      <div class="item" v-for="item in resultByName" :key="item.id">
        <div class="name">{{ item.name }}</div>
        <div class="type">{{ item.type }}</div>
        <!-- Вывожу либо жанр либо категорию -->
        <div class="category-genre">
          {{ item.category ? item.category : item.genre }}
        </div>
        <div class="link">{{ item.pageUrl }}</div>
        <div></div>
      </div>
    </div>

    <div class="buttons">
      <div class="more" @click="nextPage">Загрузить еще</div>
    </div>
  </div>
</template>

<script>
//Для обращения на сервер
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      videos: [], //Массив видосов
      lastPage: null, //Ласт стр.
      page: 0, //Текущая страница
      result: [], //Для вывода видосов
      search: "", //Для фильтра
      isError: false, //Ошибка запроса
    };
  },

  //В лайфхук добавляю обращение на сервер
  async created() {
    try {
      // Промис запрос на сервер
      const res = await axios.get(`http://localhost:3000/video`);
      this.videos = res.data;
      this.lastPage = this.videos.length;
      this.isError = false;
      //Отображаем первую страницу
      this.getPage(this.videos[this.page]);
      //Вывод ошибки
    } catch (e) {
      this.isError = true;
    }
  },

  methods: {
    //Загрузить еще
    nextPage() {
      //Обновляем данные
      if (this.page == this.lastPage - 1) {
        this.result = [];
        this.page = 0;
        this.getPage(this.videos[this.page]);
      }
      //Подгружаем еще
      else {
        this.page++;
        this.getPage(this.videos[this.page]);
      }
    },

    //Получаю данные для вывода
    getPage(mas) {
      for (let i = 0; i < 10; i++) {
        this.result.push({
          name: mas.content[i].name,
          type: mas.content[i].type,
          category: mas.content[i].category ? mas.content[i].category : null,
          genre: mas.content[i].genre ? mas.content[i].genre : null,
          pageUrl: mas.content[i].pageUrl,
        });
      }
    },
  },

  //Фильтрация
  computed: {
    resultByName() {
      return this.result.filter(
        (item) => item.name.indexOf(this.search) !== -1
      );
    },
  },
};
</script>

<style lang="scss">

#app {
  font-family: "Gill Sans", "Gill Sans MT", Calibri, "Trebuchet MS", sans-serif;
  font-weight: 600;

  .title {
    font-size: 32pt;
  }

  .error {
    color: rgb(255, 86, 86);
    font-size: 26pt;
  }

  .noError {
    display: none;
  }

  .header {
    height: 100px;
    display: flex;
    align-items: center;
    
    .page,
    .find {
      font-size: 16pt;
      margin-right: 20px;
    }
  }

  .item {
    display: flex;
    .link,
    .type,
    .name,
    .category-genre {
      height: 4em;
    }
    
    .name,
    .link {
      width: 230px;
    }
    .category-genre,
    .type {
      width: 100px;
    }
  }

  .more {
    width: 150px;
    display: flex;
    justify-content: center;
    padding: 10px 20px;
    background-color: rgb(179, 174, 174);
    cursor: pointer;
    user-select: none;
    border-radius: 3px;
    &:hover {
      opacity: 0.9;
    }
    &:active {
      background-color: rgb(99, 99, 99);
    }
  }
}
</style>
