<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
let taskAdd = ref();
let tasks = ref([]);
const addTask = () => {
  tasks.value.push({ text: taskAdd.value, checked: false });
  taskAdd.value = '';
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
};
function handleCheck(index) {
  tasks.value[index].checked = !tasks.value[index].checked;
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
}
function handleDelete(index) {
  tasks.value.splice(index, 1);
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
}

function confirmDelete(index) {
  confirmBox("Êtes-vous sûr de vouloir supprimer cette tâche ?").then((confirmation) => {
    if (confirmation) {
      handleDelete(index);
    }
  });
}


function confirmBox(message) {
  return new Promise((resolve) => {
    const confirmContainer = document.createElement("div");
    confirmContainer.classList.add("fixed", "top-0", "left-0", "w-full", "h-full", "flex", "justify-center", "items-center", "bg-black", "bg-opacity-50", "z-50");
    confirmContainer.innerHTML = `
      <div class="bg-white rounded-lg p-6">
        <p class="mb-4">${message}</p>
        <div class="flex justify-end">
          <button class="mr-2 px-4 py-2 bg-green-500 text-white rounded-md" id="confirmYes">Oui</button>
          <button class="px-4 py-2 bg-red-500 text-white rounded-md" id="confirmNo">Non</button>
        </div>
      </div>
    `;

    const confirmYesBtn = confirmContainer.querySelector("#confirmYes");
    const confirmNoBtn = confirmContainer.querySelector("#confirmNo");

    confirmYesBtn.addEventListener("click", () => {
      resolve(true);
      confirmContainer.remove();
    });

    confirmNoBtn.addEventListener("click", () => {
      resolve(false);
      confirmContainer.remove();
    });

    document.body.appendChild(confirmContainer);
  });
}


onMounted(() => {
  let savedTasks = localStorage.getItem('tasks');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});
</script>

<template>
  <main>
    <form @submit.prevent="addTask" class="flex m-2 space-x-3 justify-center pb-4">
      <label for="task">Tâche à ajouter:</label>
      <input type="text" name="task" id="task" v-model="taskAdd" class="border-2">
      <input type="submit" value="Ajouter"
        class="bg-blue-500 p-2 text-white rounded hover:cursor-pointer font-bold hover:bg-blue-800 transition">
    </form>
    <ul id="taskList" class="flex flex-col mx-auto w-96 space-y-2">
      <li v-for="(task, index) in tasks" :key="index" class="flex px-4 py-2 bg-gray-300 items-center rounded-full">
        <input type="checkbox" :name="task.text" :id="task.text" v-model="task.checked" @click="handleCheck(index)"
          class="inline-block mr-2">
        <label :for="task.text" class="inline-block font-semibold text-lg">{{
          task.text
        }}</label><button @click="confirmDelete(index)"
          class="font-bold text-red-500 bg-black rounded p-2 inline-block ml-auto hover:bg-red-500 hover:text-black transition">Delete</button>
      </li>
    </ul>
  </main>
</template>