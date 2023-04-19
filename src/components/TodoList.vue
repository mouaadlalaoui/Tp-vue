<template>
  <div>
    <h1>Todolist</h1>
    <p>Total : {{ totalTodos }}</p>
    <p>Selectionnés : {{ selectedTodos.length }}</p>

    <form v-on:submit.prevent="addTodo">
      <label>
        Titre :
        <input v-model="newTodo.title" required />
      </label>
      <label>
        Heures estimées :
        <input v-model.number="newTodo.estimatedHours" type="number" min="0" required />
      </label>
      <label>
        Responsable :
        <select v-model="newTodo.responsible" required>
          <option value="">Choisissez un responsable</option>
          <option v-for="person in people" :key="person.id" :value="person.name">
            {{ person.name }}
          </option>
        </select>
      </label>
      <button type="submit">Ajouter</button>
    </form>

    <div>
      <label>
        Filtre :
        <select v-model="filter">
          <option value="">Tous</option>
          <option value="enAttente">En attente</option>
          <option value="enCours">En cours</option>
          <option value="termine">Terminé</option>
        </select>
      </label>
      <button v-on:click="clearCompleted">Supprimer les tâches terminées</button>
    </div>

    <ul>
      <li v-for="todo in filteredTodos" :key="todo.id">
        {{ todo.title }} - {{ todo.estimatedHours }}h - {{ todo.responsible }}
        <button v-on:click="deleteTodo(todo)">Supprimer</button>
        <button v-on:click="editTodo(todo)">Editer</button>
        <input type="checkbox" v-model="todo.completed" v-on:change="toggleCompleted(todo)" />
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      todos: [
        {
          id: 1,
          title: "Faire les courses",
          estimatedHours: 1,
          responsible: "Jean",
          completed: false,
        },
        {
          id: 2,
          title: "Faire le ménage",
          estimatedHours: 2,
          responsible: "Marie",
          completed: false,
        },
        {
          id: 3,
          title: "Aller chez le médecin",
          estimatedHours: 1.5,
          responsible: "Pierre",
          completed: true,
        },
      ],
      newTodo: {
        title: "",
        estimatedHours: 0,
        responsible: "",
        completed: false,
      },
      filter: "",
      people: [
        { id: 1, name: "Jean" },
        { id: 2, name: "Marie" },
        { id: 3, name: "Pierre" },
        { id: 4, name: "Lucie" },
        { id: 5, name: "Paul" },
      ],
    };
  },
  computed: {
    filteredTodos: function() {
      if (this.filter === "") {
        return this.todos;
      } else if (this.filter === "enAttente") {
        return this.todos.filter(function(todo) {
          return !todo.completed;
        });
      } else if
      (this.filter === "enCours") {
        return this.todos.filter(function(todo) {
          return !todo.completed;
        });
      } else if (this.filter === "termine") {
        return this.todos.filter(function(todo) {
          return todo.completed;
        });
      }
      else {
  return this.todos;
}
    },
    totalTodos: function() {
      return this.todos.length;
    },
    selectedTodos: function() {
      return this.todos.filter(function(todo) {
        return todo.completed;
      });
    },
  },
  methods: {
    addTodo: function() {
      if (
        this.newTodo.title !== "" &&
        !isNaN(this.newTodo.estimatedHours) &&
        this.newTodo.responsible !== "" &&
        !this.hasTooManyTasks(this.newTodo.responsible) &&
        !this.hasTooManyHours(this.newTodo.responsible, this.newTodo.estimatedHours)
      ) {
        this.newTodo.id = this.getNextTodoId();
        this.todos.push(this.newTodo);
        this.newTodo = {
          title: "",
          estimatedHours: 0,
          responsible: "",
          completed: false,
        };
      } else {
        alert("Veuillez remplir tous les champs correctement.");
      }
    },
    deleteTodo: function(todo) {
      var index = this.todos.indexOf(todo);
      if (index !== -1) {
        this.todos.splice(index, 1);
      }
    },
    editTodo: function(todo) {
      this.newTodo = Object.assign({}, todo);
      this.deleteTodo(todo);
    },
    toggleCompleted: function(todo) {
      if (todo.completed) {
        todo.completedAt = new Date();
      } else {
        todo.completedAt = null;
      }
    },
    clearCompleted: function() {
      this.todos = this.todos.filter(function(todo) {
        return !todo.completed;
      });
    },
    getNextTodoId: function() {
      return this.todos.reduce(function(maxId, todo) {
        return Math.max(maxId, todo.id);
      }, 0) + 1;
    },
    hasTooManyTasks: function(responsible) {
      var tasks = this.todos.filter(function(todo) {
        return todo.responsible === responsible && !todo.completed;
      });
      return tasks.length >= 3;
    },
    hasTooManyHours: function(responsible, estimatedHours) {
      var tasks = this.todos.filter(function(todo) {
        return todo.responsible === responsible && !todo.completed;
      });
      var totalHours = tasks.reduce(function(sum, task) {
        return sum + task.estimatedHours;
      }, 0);
      return totalHours + estimatedHours > 10;
    },
  },
};
</script>

