<template>
  <div class="area">
    <div class="area__header content">
      <img src="@/assets/logo-negativo.svg" class="area__header__img" />
      <router-link to="/" class="area__header__sair"> Sair</router-link>
    </div>
    <h1 class="area__title content">Plataforma de Horas Complementares</h1>

    <table class="area__table content">
      <thead>
        <tr>
          <th>Nome do Evento</th>
          <th>Carga Horária do Evento</th>
          <th>Data do Evento</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="evento in eventos" :key="evento.id">
          <td>{{ evento.nome }}</td>
          <td>{{ evento.horas }}</td>
          <td>{{ evento.data }}</td>
          <td>
            <a
              @click="(modalEditar = true), carregarInfo($event, evento)"
              href="#"
            >
              <img src="@/assets/lapis.png" class="area__table__editar" />
            </a>
            <a @click="deletarEvento($event, evento.id)" href="#">
              <img src="@/assets/excluir.png" class="area__table__excluir" />
            </a>
          </td>
        </tr>
      </tbody>
    </table>
    <div class="area__buttons content">
      <!-- <button class="area__buttons__voltar">Voltar</button> -->
      <button @click="modal = true" class="area__buttons__adicionar">
        Adicionar Evento
      </button>
    </div>

    <Modal v-if="modal">
      <div class="modal">
        <div class="modal__header">
          <h3 class="modal__header__title">Cadastrar novo evento</h3>
          <a @click="modal = false" href="#">
            <img src="@/assets/fechar.svg" alt="" />
          </a>
        </div>
        <form class="modal__form">
          <input
            type="text"
            placeholder="Nome do Evento"
            class="modal__form__input"
            v-model="nome"
          />
          <input
            type="text"
            placeholder="Carga Horária"
            class="modal__form__input"
            v-model="horas"
          />
          <input
            type="date"
            placeholder="Data do Evento"
            class="modal__form__input"
            v-model="data"
          />
          <button @click="addEvento" class="modal__form__btn">Adicionar</button>
        </form>
      </div>
    </Modal>

    <Modal v-if="modalEditar">
      <div class="modal">
        <div class="modal__header">
          <h3 class="modal__header__title">Editar dados do evento</h3>
          <a href="#" @click="modalEditar = false">
            <img src="@/assets/fechar.svg" />
          </a>
        </div>
        <form class="modal__form">
          <input
            type="text"
            placeholder="Nome do evento"
            v-model="editar.nome"
            class="modal__form__input"
          />
          <input
            type="text"
            placeholder="Carga Horária"
            v-model="editar.horas"
            class="modal__form__input"
          />
          <input
            type="date"
            placeholder="Data do Evento"
            v-model="editar.data"
            class="modal__form__input"
          />
          <button @click="modalEditar = false" class="modal__form__btn">
            Editar
          </button>
        </form>
      </div>
    </Modal>
  </div>
</template>

<script>
import Modal from "@/components/Modal";
import axios from "axios";
export default {
  data() {
    return {
      nome: "",
      horas: "",
      data: "",
      modal: false,
      modalEditar: false,
      eventos: [],
      editar: {
        nome: "",
        horas: "",
        data: "",
      },
    };
  },
  components: {
    Modal,
  },
  mounted() {
    this.carregarEventos();
  },
  methods: {
    async addEvento(e) {
      e.preventDefault();
      if (this.nome === "" || this.horas === "" || this.data === "") {
        alert("Todos os campos são obrigatórios!");
      } else {
        const { data } = await axios.post("http://localhost:3000/eventos", {
          nome: this.nome,
          horas: this.horas,
          data: this.data,
        });
        this.modal = false;

        this.carregarEventos();
        this.nome = "";
        this.horas = "";
        this.data = "";
      }
    },
    async carregarEventos() {
      const { data } = await axios.get("http://localhost:3000/eventos");
      this.eventos = data;
    },

    carregarInfo(e, evento) {
      e.preventDefault();
      this.modalEditar = true;
      this.editar.id = evento.id;
      this.editar.nome = evento.nome;
      this.editar.horas = evento.horas;
      this.editar.data = evento.data;
    },
    async editarEvento(e) {
      e.preventDefault();
      if (this.nome === "" || this.horas === "" || this.data === "") {
        alert("Todo os campos são obrigatórios!");
      } else {
        const { data } = await axios.put(
          `http://localhost:3000/eventos/${this.editar.id}`,
          {
            nome: this.editar.nome,
            horas: this.editar.horas,
            data: this.editar.data,
          }
        );
        this.modalEditar = false;
        this.carregarEventos();
      }
    },

    async deletarEvento(e, id) {
      e.preventDefault();

      const { data } = await axios.delete(
        `http://localhost:3000/eventos/${id}`
      );

      this.carregarEventos();
    },
    async carregarEventos() {
      const { data } = await axios.get("http://localhost:3000/eventos");
      this.eventos = data;
    },
  },
};
</script>

<style lang="scss" scoped>
.content {
  margin: 0 auto;
  width: 70%;
}
.area {
  display: flex;
  flex-direction: column;
  background: #212121;
  width: 100%;
  height: 100%;
  &__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 30px 0;
    &__sair {
      color: #dfdbdb;
      text-decoration: none;
      cursor: pointer;
    }
  }
  &__title {
    color: #dfdbdb;
    margin: 10px auto 40px;
  }
  &__table {
    background: #303030;
    border-radius: 5px;
    border-collapse: collapse;
    thead {
      tr {
        th {
          padding: 10px 0;
          color: #dfdbdb;
          border-bottom: 1px solid #000;
        }
      }
    }
    tbody {
      tr {
        td {
          padding: 10px 0;
          color: #dfdbdb;
          text-align: center;
        }
      }
    }
  }
  &__buttons {
    display: flex;
    justify-content: flex-end;
    margin-top: 20px;
    &__adicionar {
      border: 0;
      background: rgba(228, 171, 0, 0.7);
      border-radius: 5px;
      color: #fff;
      padding: 10px;
      width: 182px;
      margin: 10px;
      transition: 800ms;
      cursor: pointer;
    }
  }
}
.modal {
  display: flex;
  flex-direction: column;
  padding: 15px;
  width: 100%;
  &__header {
    display: flex;
    justify-content: space-between;
    padding: 10px;
    &__title {
      color: #fff;
    }
  }
  &__form {
    display: flex;
    flex-wrap: wrap;
    padding: 10px;
    &__editar {
      margin-right: 10px;
    }
    &__excluir {
      margin-right: 10px;
    }
    &__input {
      outline: 0;
      padding: 10px;
      width: calc(50% - 5px);
      margin-bottom: 10px;
      border-radius: 5px;
      border: 0;
      &:nth-child(1) {
        margin-right: 5px;
      }
      &:nth-child(3) {
        margin-right: 5px;
      }
    }
    &__btn {
      width: 100px;
      height: 40px;
      border: 0;
      border-radius: 5px;
      background: rgba(228, 171, 0, 0.7);
      font-weight: 500;
      color: #fff;
      cursor: pointer;
      outline: 0;
    }
  }
}
</style>
