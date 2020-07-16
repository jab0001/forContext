<template>
  <div class="page">
    <header class="header">
      <h1 class="header__title">TODO-List</h1>
      <div class="header__wrapper">
        <div class="header__wrapper-btn">
          <button class="header__btn" @click="addCard(false)">Create New Task</button>
          <button class="header__btn" @click="addGroup">Create New Tasks List</button>
          <button class="header__btn" @click="clearFilters">Reset Filters</button>
        </div>
        <TodoFilter v-model="filters" />
      </div>
    </header>
    <main>
      <div class="todos">
        <component
          :is="item.class"
          v-for="item in getFilteredList"
          :key="item.id"
          :item="item"
          @remove-card="handleRemoveCard"
          @remove-group="handleRemoveGroup"
          @add-card="addCard"
          @move-card="moveCard"
        />
      </div>
      <v-dialog />
    </main>
  </div>
</template>

<script>
import Todo from "@/components/Todo";
import TodoGroup from "@/components/TodoGroup";
import TodoFilter from "@/components/TodoFilter";

export default {
  name: "MainWrapper",
  props: {},
  components: {
    TodoFilter
  },
  data() {
    return {
      filters: {
        name: "",
        status: false
      },
      list: [
        {
          id: Math.random(),
          name: "todo1",
          status: 0,
          class: Todo,
          drag: false,
          description: ""
        },
        {
          id: Math.random(),
          name: "todo-list1",
          class: TodoGroup,
          items: [
            {
              id: Math.random(),
              name: "todo2",
              status: 1,
              class: Todo,
              drag: true,
              description: ""
            },
            {
              id: Math.random(),
              name: "todo3",
              status: 1,
              class: Todo,
              drag: true,
              description: ""
            }
          ]
        },
        {
          id: Math.random(),
          name: "todo-list2",
          class: TodoGroup,
          items: [
            {
              id: Math.random(),
              name: "todo4",
              status: 1,
              class: Todo,
              drag: true,
              description: ""
            },
            {
              id: Math.random(),
              name: "todo5",
              status: 1,
              class: Todo,
              drag: true,
              description: ""
            }
          ]
        }
      ]
    };
  },
  computed: {
    getFilteredList() {
      if (!this.filters.name && this.filters.status === false) {
        return this.list;
      }

      const isCard = function has(item) {
        return item ? hasOwnProperty.call(item, "status") : false;
      };

      return this.list
        .filter(item => {
          if (isCard(item)) {
            return this.applyFilters(item);
          } else {
            return item.items.some(card => this.applyFilters(card));
          }
        })
        .map(item => {
          if (isCard(item)) return item;

          const clone = Object.assign({}, item);

          clone.items = clone.items.filter(card => this.applyFilters(card));

          return clone;
        });
    }
  },
  methods: {
    addCard(groupId) {
      const card = {
        id: Math.random(),
        name: `todo${this.list.length + 1}`,
        status: 0,
        class: Todo,
        drag: !!groupId,
        description: ""
      };

      if (groupId) {
        const index = this.list.findIndex(({ id }) => id === groupId);
        this.list[index].items.push(card);
        return;
      }

      this.list.push(card);
    },

    moveCard({ oldIndex, newIndex, oldId, newId }) {
      if (oldIndex === newIndex && oldId === +newId) return;

      const oldGroupIndex = this.list.findIndex(({ id }) => id === oldId);
      const newGroupIndex = this.list.findIndex(({ id }) => id === +newId);

      const tempCard = this.list[oldGroupIndex].items.splice(oldIndex, 1);

      this.list[newGroupIndex].items.splice(newIndex, 0, tempCard[0]);
    },

    removeCard(data) {
      if (typeof data === "object") {
        const { groupId, index } = data;
        const groupIndex = this.list.findIndex(({ id }) => id === groupId);
        this.list[groupIndex].items.splice(index, 1);
        return;
      }

      const index = this.list.findIndex(({ id }) => id === data);
      this.list.splice(index, 1);
    },

    addGroup() {
      const id = Date.now();
      const group = {
        id,
        name: `TodosGroup${this.list.length + 1}`,
        class: TodoGroup,
        items: []
      };

      this.list.push(group);
    },

    removeGroup(groupId) {
      const index = this.list.findIndex(({ id }) => id === groupId);
      this.list.splice(index, 1);
    },

    handleRemoveCard(data) {
      this.$modal.show("dialog", {
        title: "Delete Todo",
        text: "Are you sure?",
        buttons: [
          {
            title: "Yes",
            handler: () => {
              this.removeCard(data);
              this.$modal.hide("dialog");
            }
          },
          {
            title: "No"
          }
        ]
      });
    },

    handleRemoveGroup(id) {
      this.$modal.show("dialog", {
        title: "Delete TodoList",
        text: "Are you sure?",
        buttons: [
          {
            title: "Yes",
            handler: () => {
              this.removeGroup(id);
              this.$modal.hide("dialog");
            }
          },
          {
            title: "No"
          }
        ]
      });
    },

    clearFilters() {
      this.filters = {
        name: "",
        status: false
      };
    },

    applyFilters(item) {
      const textFilter = this.filters.name
        .toLowerCase()
        .split(" ")
        .join("");
      const statusFilter = this.filters.status;

      const filters = {
        name: name => name.toLowerCase().includes(textFilter),
        status: status =>
          statusFilter === false ? true : status === statusFilter
      };

      const filtersKey = Object.keys(filters);

      return filtersKey.every(key => filters[key](item[key]));
    }
  },
  watch: {},
  mounted() {}
};
</script>

<style lang="scss">
.page {
  max-width: 900px;
  margin: 0 auto;
}

.header {
  margin: 0 auto;
  text-align: center;

  &__wrapper {
    display: flex;
  }
}

.header__btn {
  background: rgba(48, 65, 218, 0.445);
  border: none;
  border-radius: 15px;
  min-height: 35px;
  padding: 10px;
  outline: none;
  cursor: pointer;

  &:hover {
    background: rgba(48, 65, 218);
    color: #ffffff;
  }

  &:active {
    background: #3fee65;
  }
}

.header__wrapper-btn {
  display: flex;
  justify-content: space-between;
  width: 500px;
  margin: 0 auto;
}

.todos {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

@media (max-width: 767px) {
  .header__wrapper {
    display: flex;
    flex-wrap: wrap;
  }

  .header__btn {
    width: 85px;
  }
}
</style>