<template>
    <header>
        <h1>ToDo List</h1>
    </header>

    <main>
        <section class="container">
            <article class="header">
                <div class="time">
                    <h2 class="date">
                        {{ date.weekday }},
                        <span>{{ date.dayNumber }}st</span>
                    </h2>
                    <p>{{ date.month }}</p>
                </div>
                <p>
                    <span>{{ todos.length }}</span>
                    {{ todos.length > 1 ? 'tasks' : 'task' }}
                </p>
            </article>

            <article class="todos">
                <h2 v-if="!todos.length" class="no-todos">No tasks</h2>
                <ul v-else class="todo-list">
                    <li v-for="todo in todos" :key="todo.id" class="todo">
                        <span
                            class="task-text"
                            :class="{ completed: todo.completed }"
                        >
                            <h2 class="task-title">{{ todo.task }}</h2>
                            <p class="task-description">
                                {{ todo.description }}
                            </p>
                        </span>
                        <div class="wrapper-actions">
                            <button type="button">✏️</button>
                            <label class="custom-checkbox">
                                <input
                                    type="checkbox"
                                    v-model="todo.completed"
                                    name="completed"
                                />
                                <span class="checkmark"></span>
                            </label>

                            <button type="button" @click="deleteTodo(todo.id)">
                                🗑️
                            </button>
                        </div>
                    </li>
                </ul>
            </article>
            <div class="button-wrapper">
                <button type="button" class="left-btn" @click="showAllTodos">
                    📋
                </button>
                <button type="button" @click="completedTodo" class="right-btn">
                    ✅
                </button>
            </div>

            <button
                type="button"
                class="open-form-btn"
                @click="openForm"
            ></button>
        </section>

        <transition name="fade-slide">
            <article class="form" v-if="active">
                <form @submit.prevent="onSubmit" class="form-box">
                    <label>
                        Title:
                        <input
                            type="text"
                            name="task"
                            v-model="todo.task"
                            placeholder="Title"
                        />
                    </label>
                    <label>
                        Description:
                        <input
                            type="text"
                            name="description"
                            v-model="todo.description"
                            placeholder="Description"
                        />
                    </label>

                    <button type="submit" class="form-btn">Add todo</button>
                </form>
            </article>
        </transition>
    </main>
</template>

<script lang="ts">
type Todo = {
    id: number;
    task: string;
    description: string;
    completed: boolean;
};

const dateData = new Date();
export default {
    data() {
        return {
            date: {
                month: new Intl.DateTimeFormat('en-US', {
                    month: 'long',
                }).format(dateData),
                dayNumber: dateData.getDate(),
                weekday: new Intl.DateTimeFormat('en-US', {
                    weekday: 'long',
                }).format(dateData),
            },
            active: false,
            todo: {
                task: '',
                description: '',
            },
            allTodos: [
                {
                    id: 1,
                    task: 'Learn Vue',
                    description: 'Learn Vue',
                    completed: true,
                },
                {
                    id: 2,
                    task: 'Start project',
                    description: 'Learn Vue',
                    completed: false,
                },
                {
                    id: 3,
                    task: 'Done project',
                    description: 'Learn Vue',
                    completed: false,
                },
            ] as Todo[],
            todos: [] as Todo[],
            createTodo(todo: Omit<Todo, 'id' | 'completed'>) {
                this.allTodos.push({
                    id: this.allTodos.length + 1,
                    completed: false,
                    ...todo,
                });
                this.todos = [...this.allTodos];
            },
            isCompleted(todos: Todo[]) {
                return todos.filter(todo => todo.completed === false);
            },
            completedTodo() {
                this.todos = this.allTodos.filter(
                    todo => todo.completed === true
                );
                this.isCompleted(this.todos);
            },
            showAllTodos() {
                this.todos = [...this.allTodos];
            },
            deleteTodo(id: number) {
                this.todos = this.todos.filter(todo => todo.id !== id);
                this.allTodos = this.allTodos.filter(todo => todo.id !== id);
            },
            onSubmit() {
                if (!this.todo.task) this.todo.task = 'No task';
                if (!this.todo.description)
                    this.todo.description = 'No description';
                this.todos = [...this.allTodos];
                this.createTodo(this.todo);
                this.formReset();
                this.openForm();
            },
            formReset() {
                this.todo.task = '';
                this.todo.description = '';
            },
            openForm() {
                this.active = !this.active;
            },
        };
    },
    mounted() {
        this.todos = [...this.allTodos];
    },
};
</script>

<style scoped>
.active {
    display: block;
}

.container {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: #fff;
    margin-inline: auto;
    border-radius: 12px;
    gap: 1rem;
    min-height: 670px;
    max-width: 490px;
    width: 100%;
}

.header {
    display: flex;
    width: 100%;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    border-bottom: 1px solid #dedee0;
    padding: 24px;

    .time {
        color: #9591bf;
    }

    .date {
        margin-bottom: 0.5rem;
    }

    p {
        color: #c7c7c9;
    }
}

.todos {
    display: flex;
    justify-content: center;
    width: 100%;
    max-height: 492px;
    overflow: overlay;
    flex: 1;

    .no-todos {
        font-weight: 600;
        font-size: 2.5rem;
        color: #6e62ef;
        align-content: center;
    }
}
.todo-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
    width: 100%;
}
.button-wrapper {
    display: flex;
    width: 100%;
    justify-content: space-between;
    height: 100%;
    border-top: 1px solid #dedee0;

    button {
        height: 68px;
        width: 40%;
        font-size: 2rem;
    }

    .left-btn {
    }
    .right-btn {
    }
}
.todo {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-inline: 24px;
}
.task-title {
    font-weight: 500;
    color: #878787;
    font-size: 1.5rem;
}
.task-description {
    color: #878787;
}
.wrapper-actions {
    display: flex;
    gap: 8px;
    font-size: 2rem;
}

.task-title,
.task-description {
    position: relative;
    transition:
        width 0.3s ease,
        opacity 0.3s ease;
}

.task-title::after,
.task-description::after {
    content: '';
    position: absolute;
    left: 0;
    top: 50%;
    height: 2px;
    background-color: #525454;
    width: 0;
}

.completed .task-title,
.completed .task-description {
    opacity: 0.6;
}

.completed .task-title::after,
.completed .task-description::after {
    width: 100%;
}

.open-form-btn {
    position: absolute;
    bottom: -24px;
    background-color: #42bd88;
    width: 68px;
    height: 68px;
    border-radius: 50%;
    cursor: pointer;
    box-shadow:
        0 4px 12px rgba(66, 189, 136, 0.6),
        0 0 8px rgba(66, 189, 136, 0.4);
    transition: box-shadow 0.3s ease;

    &:hover {
        box-shadow:
            0 6px 20px rgba(66, 189, 136, 0.8),
            0 0 12px rgba(66, 189, 136, 0.6);
    }

    &::before {
        content: '';
        width: 2px;
        height: 34px;
        background-color: #fff;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }
    &::after {
        content: '';
        width: 34px;
        height: 2px;
        background-color: #fff;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }
}

.form {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 100%;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgba(169, 169, 169, 0.4);
}

.form-box {
    display: flex;
    flex-direction: column;
    margin-top: 16px;
    padding: 20px;
    border: 1px solid #ccc;
    background-color: #f9f9f9;
    gap: 16px;
    border-radius: 12px;
    box-shadow: 0 4px 16px rgba(66, 189, 136, 0.2);
    height: 520px;
    width: 320px;

    label {
        display: flex;
        flex-direction: column;
        font-weight: 600;
        font-size: 1.5rem;
        margin-block: 0.5rem;
        color: #6e62ef;
    }

    input {
        margin-top: 6px;
        padding: 10px 14px;
        font-size: 15px;
        border: 1px solid #ccc;
        border-radius: 8px;
        background-color: #fff;
        transition:
            border-color 0.3s ease,
            box-shadow 0.3s ease;
        outline: none;
        resize: vertical;

        &::placeholder {
            color: #999;
        }

        &:focus,
        &:hover {
            border-color: #42bd88;
            box-shadow: 0 0 6px rgba(66, 189, 136, 0.3);
        }
    }
}

.form-btn {
    background-color: #42bd88;
    color: #fff;
    font-size: 16px;
    font-weight: 500;
    padding: 12px 24px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    box-shadow:
        0 4px 12px rgba(66, 189, 136, 0.6),
        0 0 8px rgba(66, 189, 136, 0.4);

    transition:
        background-color 0.3s ease,
        transform 0.2s ease,
        box-shadow 0.3s ease;

    &:hover {
        background-color: #36a773;
        transform: translateY(-2px);
        box-shadow:
            0 6px 20px rgba(66, 189, 136, 0.8),
            0 0 12px rgba(66, 189, 136, 0.6);
    }

    &:active {
        transform: translateY(0);
        box-shadow:
            0 2px 6px rgba(66, 189, 136, 0.5),
            0 0 4px rgba(66, 189, 136, 0.3);
    }
}
.fade-slide-enter-active,
.fade-slide-leave-active {
    transition: opacity 0.4s ease;
}

.fade-slide-enter-from {
    opacity: 0;
}

.fade-slide-enter-to {
    opacity: 1;
}

.fade-slide-leave-from {
    opacity: 1;
}

.fade-slide-leave-to {
    opacity: 0;
}

.custom-checkbox {
    display: flex;
    align-items: center;
    gap: 8px;
    cursor: pointer;
    user-select: none;
    font-weight: 500;
    color: #333;
    position: relative;
}

.custom-checkbox input {
    position: absolute;
    opacity: 0;
    cursor: pointer;
}

.custom-checkbox .checkmark {
    width: 32px;
    height: 32px;
    border: 2px solid #ccc;
    border-radius: 50%;
    display: inline-block;
    position: relative;
    transition: all 0.2s ease;
}

.custom-checkbox input:checked + .checkmark {
    background-color: #42bd88;
    border-color: #42bd88;
}

.custom-checkbox input:checked + .checkmark::after {
    content: '';
    position: absolute;
    left: 12px;
    top: 5px;
    width: 8px;
    height: 16px;
    border: solid white;
    border-width: 0 3px 3px 0;
    transform: rotate(45deg);
}
</style>
