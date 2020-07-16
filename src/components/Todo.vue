<template>
  <div class="todo">
    <div class="todo__header">
      <input
        v-model="item.status"
        type="checkbox"
        :true-value="1"
        :false-value="0"
        class="todo__chk"
      />
      <span v-if="item.drag" class="todo__btn-move task__move">move</span>
      <input v-model="item.name" placeholder="Task name" type="text" class="todo__input" />
      <button class="todo__btn-remove" @click="removeCard">Remove Task</button>
    </div>
    <div class="todo__info">
      <textarea
        ref="description"
        v-model="item.description"
        :style="moreHeight"
        class="todo__description"
        placeholder="Description"
      ></textarea>
      <span
        v-if="descriptionOverflow"
        class="todo__decription-opener"
        @click="showDescriptionMore"
      >More</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "Todo",
  props: {
    item: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      descriptionOverflow: false,
      descriptionMoreHeight: 0
    };
  },
  computed: {
    moreHeight() {
      return {
        "min-height": this.descriptionMoreHeight
      };
    }
  },
  watch: {
    "item.description": {
      handler() {
        this.checkDescriptionOverflow();
      }
    }
  },
  methods: {
    removeCard() {
      this.$emit("remove-card", this.item.id);
    },
    checkDescriptionOverflow() {
      this.descriptionOverflow =
        this.$refs.description.scrollHeight >
        this.$refs.description.clientHeight;
    },

    showDescriptionMore() {
      this.descriptionMoreHeight = `${this.$refs.description.scrollHeight}px`;
      this.descriptionOverflow = false;
    }
  }
};
</script>

<style lang="scss">
.todo {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: 3px solid #5bbeec;
  border-radius: 15px;
  width: 100%;
  margin-top: 10px;
  background: #ffffff;

  &__header {
    width: 98%;
    margin: 0 auto;
    margin-top: 5px;
    margin-bottom: 15px;
    display: flex;
    justify-content: space-between;
  }

  &__description {
    width: 95%;
    display: block;
    margin: 0 auto;
    height: 88px;
    margin-bottom: 10px;
    border-radius: 25px;
    padding-left: 25px;
    box-sizing: border-box;
  }

  &__btn-remove {
    border-radius: 25px;
    border: none;
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

  &__btn-move {
    cursor: pointer;
    &:hover {
      text-transform: uppercase;
    }
    &:active {
      color: green;
    }
  }
}

@media (max-width: 767px) {
  .todo__input {
    width: 50px;
  }
}
</style>
