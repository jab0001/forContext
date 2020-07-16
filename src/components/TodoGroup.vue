<template>
  <div class="todos-list">
    <div class="todos-list__item">
      <div class="todos-list__title">{{ item.name }}</div>
      <div class="todos-list__wrapper-btn">
        <button class="todos-list__btn" @click="addCard">Add Task</button>
        <button class="todos-list__btn" @click="removeGroup">Remove List</button>
      </div>
    </div>
    <div ref="todosGroup" class="todos-list__wrapper" :data-id="item.id">
      <component
        :is="card.class"
        v-for="card in item.items"
        :key="card.id"
        :item="card"
        @remove-card="removeCard"
      />
    </div>
  </div>
</template>

<script>
import Sortable from "sortablejs";

export default {
  name: "TodoGroup",
  props: {
    item: {
      type: Object,
      required: true
    }
  },
  mounted() {
    new Sortable(this.$refs["todosGroup"], {
      group: "cart",
      animation: 150,
      handle: ".task__move",
      onEnd: evt => {
        this.moveCard(evt);
      }
    });
  },
  methods: {
    addCard() {
      this.$emit("add-card", this.item.id);
    },
    moveCard({ oldIndex, newIndex, to }) {
      const data = {
        oldIndex,
        newIndex,
        oldId: this.item.id,
        newId: to.dataset.id
      };

      this.$emit("move-card", data);
    },

    removeCard(cardId) {
      const index = this.item.items.findIndex(({ id }) => id === cardId);
      this.$emit("remove-card", {
        groupId: this.item.id,
        index
      });
    },

    removeGroup() {
      this.$emit("remove-group", this.item.id);
    }
  }
};
</script>

<style lang="scss">
.todos-list {
  padding: 15px;
  margin-top: 10px;
  border: 5px solid #3fee65;
  border-radius: 25px;
  width: 45%;
  flex-wrap: wrap;
  background: #ffffff;

  &__wrapper-btn {
    width: 100%;
    display: flex;
    justify-content: space-between;
  }

  &__btn {
    border-radius: 5px;
    border: none;
    min-height: 25px;
    background: rgb(48, 65, 218);
    outline: none;
    cursor: pointer;

    &:hover {
      background: rgb(59, 44, 190);
      color: rgb(226, 37, 69);
    }

    &:focus {
      background: rgb(226, 37, 69);
      color: #ffffff;
    }
  }

  &__title {
    text-align: center;
  }
}
@media (min-width: 768px) {
  .todos-list {
    width: 100%;
    box-sizing: border-box;
  }
}

@media (max-width: 767px) {
  .todos-list {
    width: 100%;
    box-sizing: border-box;
  }
}
</style>
