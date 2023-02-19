<template>
  <div class="wrapper">
    <Navbar @setSearch="searchValue = $event" />
    <Notes 
      @delNote="delNote" 
      :notes="filterNotes"
      @change="change"
    />
    <Modal 
      @close="isModalOpen = false" 
      v-show="isModalOpen"
      @addNote="addNote"
      :edit="edit"
      :editedNote="editedNote"
      @changedNote="changedNote"
    />
    <button 
      class="add__note" 
      @click="isModalOpen = true, edit = false" 
      v-if="!isModalOpen"
    >
      <img src="@/assets/images/edit.svg" alt="">
    </button>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import Notes from '@/components/Notes.vue'
import Modal from './components/Modal.vue';
export default {
  components: {
    Navbar,
    Notes,
    Modal
  },
  data() {
    return {
      isModalOpen: false,
      notes: [],
      edit: false,
      editedNote: null,
      searchValue: ''
    }
  },
  computed: {
    filterNotes() {
      return this.searchValue ?  this.notes.filter(note => note.title.toLowerCase().includes(this.searchValue.toLowerCase()))  : this.notes;
    }
  },
  mounted() {
    this.getNotes()
  },
  methods: {
    addNote(note) {
      this.notes.push(note)
    },
    delNote(id) {
      let index = this.notes.findIndex((note) => note.id == id)
      this.notes.splice(index,1)
    },
    change(id) {
      this.isModalOpen = this.edit = true
      let currentNote = this.notes.find(note => note.id == id)
      this.editedNote = currentNote
    },
    changedNote(newNote) {
        this.notes.forEach(note => {
          if(note.id == newNote.id) {
            note.title = newNote.title
            note.text = newNote.text
            note.date = newNote.date
          }
        })
    },
    getNotes() {
      let localNotes = localStorage.notes
      if(localNotes) {
        this.notes = JSON.parse(localNotes)
      }
    }
  },
  watch: {
    notes: {
      handler() {
        localStorage.notes = JSON.stringify(this.notes)  
      },
      deep: true
    }
  }
}
</script>
