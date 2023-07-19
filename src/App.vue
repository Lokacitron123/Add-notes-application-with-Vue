<script setup>
import { ref, onMounted, watch } from "vue";

const showModal = ref(false);
const newNote = ref("");
const errorMessage = ref("");
const savedNotes = ref([]);

function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}

const addNote = () => {
  if (newNote.value.length <= 10) {
    // Error handler checking if the note is less than 10 characters long
    errorMessage.value = "Your note needs to be 10 characters or more";
    return;
  }
  savedNotes.value.push({
    id: Math.floor(Math.random() * 1000000000),
    text: newNote.value,
    date: new Date(),
    backgroundColor: getRandomColor(),
  });
  showModal.value = false;
  newNote.value = "";
  errorMessage.value = "";
};

// Load savedNotes from localStorage on component mount
onMounted(() => {
  const savedNotesFromLocalStorage = localStorage.getItem("savedNotes");
  if (savedNotesFromLocalStorage) {
    savedNotes.value = JSON.parse(savedNotesFromLocalStorage);
  }
});

// Save savedNotes to localStorage whenever it changes
watch(savedNotes, (newVal) => {
  localStorage.setItem("savedNotes", JSON.stringify(newVal));
});
</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <textarea
          v-model.trim="newNote"
          name="note"
          id="note"
          cols="30"
          rows="10"
        ></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <div class="btn-container">
          <button class="btn-one" @click="addNote">Add Note</button>
          <button class="btn-two" @click="showModal = false">Close</button>
        </div>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="cards-container">
        <div
          v-for="savedNote in savedNotes"
          :key="savedNote.id"
          class="card"
          :style="{ backgroundColor: savedNote.backgroundColor }"
        >
          <p class="main-text">{{ savedNote.text }}</p>
          <p class="date">{{ savedNote.date.toLocaleString() }}</p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
main {
  background-color: #333;
  min-height: 100vh;
}

.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto;
}

header {
  widows: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  font-size: 4rem;
  color: #fff;
  margin-bottom: 3rem;
}

.btn-container {
  display: flex;
  justify-content: space-evenly;
}

button {
  cursor: pointer;
  border: none;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
  width: 50px;
  border-radius: 50%;
  padding: 2rem;
  background-color: aliceblue;
}

.btn-one {
  background-color: orangered;
  color: #333;
  margin: 10px;
}

.btn-two {
  background-color: yellowgreen;
  color: #333;
  margin: 10px;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
}

.card {
  width: 225px;
  height: 225px;
  background-color: rgb(237, 182, 44);
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 20px;
}

.date {
  font-size: 12.5px;
  font-weight: bold;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.77);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal {
  width: 750px;
  background-color: bisque;
  border-radius: 10px;
  padding: 3rem;
  position: relative;
  display: flex;
  flex-direction: column;
}

.modal p {
  color: red;
}
textarea {
  margin-bottom: 1rem;
}
</style>
