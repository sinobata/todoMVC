<template>
  <section class="main" v-show="todos.length" v-cloak>
    <!-- 
            v-show   ->  式の値の真偽に応じて、要素の CSS プロパティ display をトグル
            v-cloak  ->  関連付けられた Vue インスタンスのコンパイルが終了するまでの間残存します。
                        [v-cloak] { display: none } のような CSS のルールと組み合わせて、Vue インスタンス が用意されるまでの間
                        コンパイルされていない Mustache バインディングを隠すのに使うことができる
            
    -->
    <input
      class="toggle-all"
      type="checkbox"
      id="toggle-all"
      :value="allDone"
      :checked="allDone"
      @change="onInput"
    >
    <label for="toggle-all"></label>
    <ul class="todo-list">
      <li
        v-for="todo in filteredTodos"
        class="todo"
        :key="todo.id"
        :class="{completed:todo.completed, editing: todo == editedTodo}"
      >
        <!--  
                    v-for   ->  要素またはテンプレートブロックを複数回描画
                    todo in filteredTodos   ->  app.jsのcomputedからtodoを受け取っている 
        -->
        <todo-item :todo="todo" @remove-todo="onRemoveTodo" @done="done" @edit-todo="onEditTodo"></todo-item>

        <todo-edit
          :todo="todo"
          @done-edit="onDoneEdit"
          @cancel-edit="onCancelEdit"
          :editedTodo="editedTodo"
        ></todo-edit>
      </li>
    </ul>
  </section>
</template>

<script>
import TodoEdit from "./TodoEdit.vue";
import TodoItem from "./TodoItem.vue";

export default {
  name: "TodoList",
  components: {
    TodoItem,
    TodoEdit
  },
  data() {
    return {
      editedTodo: null
    };
  },
  props: {
    todos: Array,
    filteredTodos: Array,
    allDone: Boolean
  }, // app.jsからのv-model参照
  methods: {
    onRemoveTodo(todo) {
      this.$emit("remove-todo", todo);
    },
    done(todo, completed) {
      this.$emit("done", todo, completed);
    },
    onInput() {
      this.$emit("allDone", !this.allDone);
    },
    onEditTodo(todo) {
      this.editedTodo = todo; //編集ずみtodoにtodoを
    },
    onDoneEdit(todoTitle) {
      if (!this.editedTodo) {
        //editedtodoではないのなら
        return;
      }
      const title = todoTitle.trim(); //todotileをtrimする　（空白削除）
      if (title) {
        // todoが空欄じゃなかったら
        this.editedTodo.title = title; //editedに
      } else {
        this.removeTodo(this.editedTodo); //removetodoをする
      }
      this.editedTodo = null; //nullを代入=>変更なし
    },
    onCancelEdit() {
      this.editedTodo = null; // cencelされたらnullを代入=>変更なし
    }
  }
};
</script>

<style scoped>
[v-cloak] {
  display: none;
}
/*このディレクティブは関連付けられた Vue インスタンスのコンパイルが終了するまでの間残存します。
コンパイルされていない Mustache バインディングを隠すのに使うことができます。*/
</style>
