<template>
  <div id="columns">
    <!-- menu -->
    <div class="bars">
      <Menu class="menu-button" @click="menuOpen = !menuOpen" />
    </div>
    <div id="menu" v-if="menuOpen || windowWidth >= 768">
      <aside>
        <ul class="menu-list">
          <li
            class="item flex justify-between"
            @click="filterNotes = 'All notes'"
          >
            <div class="flex gap-4">
              <ScrollText width="16" height="16" />
              <span>All notes</span>
            </div>
            <span class="menu-item-count">{{ notes.length }}</span>
          </li>
          <!-- notebooks -->
          <li class="item flex gap-4" @click="newNotebook">
            <CirclePlus width="16" height="16" />
            <span>New Notebook</span>
          </li>
          <details>
            <summary>
              <Notebook width="16" height="16" />
              <span>Notebooks</span>
            </summary>
            <ul>
              <li
                class="item flex justify-between pl-8"
                v-for="notebook in notebooks"
                :key="notebook.id"
                @click="filterNotes = notebook.name"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <input id="input-edit" type="text" v-model="notebook.name" />
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount(notebook.name)
                }}</span>
              </li>
            </ul>
          </details>
          <!-- status -->
          <details>
            <summary>
              <FileCheck width="16" height="16" />
              <span>Status</span>
            </summary>
            <ul>
              <li
                class="item flex justify-between pl-8"
                @click="filterNotes = 'Draft'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <span>Draft</span>
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount("Draft")
                }}</span>
              </li>
              <li
                class="item flex justify-between pl-8"
                @click="filterNotes = 'Active'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <span>Active</span>
                </div>
                <span class="menu-item-count">
                  {{ filteredNotesCount("Active") }}
                </span>
              </li>
              <li
                class="item flex justify-between pl-8"
                @click="filterNotes = 'Completed'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <span>Completed</span>
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount("Completed")
                }}</span>
              </li>
              <li
                class="item flex justify-between pl-8"
                @click="filterNotes = 'Archived'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <span>Archived</span>
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount("Archived")
                }}</span>
              </li>
            </ul>
          </details>
          <!-- tags -->
          <li class="item flex gap-4" @click="newTag">
            <CirclePlus width="16" height="16" />
            <span>New Tag</span>
          </li>
          <details>
            <summary>
              <Tags width="16" height="16" />
              <span>Tags</span>
            </summary>
            <ul>
              <li
                class="item flex justify-between pl-8"
                v-for="tag in tags"
                :key="tag.id"
                @click="filterNotes = tag.name"
              >
                <div class="flex gap-4">
                  <Tag width="16" height="16" :color="`var(--${tag.color})`" />
                  <input id="input-edit" type="text" v-model="tag.name" />
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount(tag.name)
                }}</span>
              </li>
            </ul>
          </details>

          <li class="item flex justify-between" @click="filterNotes = 'Trash'">
            <div class="flex gap-4">
              <Trash2 width="16" height="16" />
              <span>Trash</span>
            </div>
            <span class="menu-item-count">{{
              filteredNotesCount("Trash")
            }}</span>
          </li>
        </ul>
      </aside>
    </div>
    <!-- notes -->
    <div id="notes-container">
      <div id="notes-header">
        <div id="notes-title">
          <p class="notes-title">
            {{ filterNotes }}
          </p>
          <button @click="newNote">New note</button>
        </div>
        <input
          id="search-notes"
          type="text"
          placeholder="Search notes..."
          v-model="searchNoteName"
        />
      </div>

      <div id="notes">
        <div
          @click="selectNote(note)"
          class="note"
          v-for="note in filteredNotes"
          :key="note.id"
        >
          <h2 class="note-title">{{ note.name }}</h2>
          <span
            class="tag"
            :style="`background:var(--${note.tag.color})`"
            v-if="note.tag !== ''"
            >{{ note.tag.name }}</span
          >
          <p class="note-description">
            {{ note.description }}
          </p>
        </div>
      </div>
    </div>
    <!-- textedit -->
    <div id="textedit-nonote" v-if="noteIsSelected === false">
      <CircleAlert width="72" height="72" class="text-grey" />
      <p>No note here !</p>
    </div>
    <div id="textedit-container" v-else>
      <div id="textedit-header">
        <input
          id="textedit-notename"
          class="input"
          type="text"
          placeholder="Note name"
          v-model="selectedNote.name"
        />

        <button class="secondary" @click="isPreviewMode = !isPreviewMode">
          {{ isPreviewMode ? "Edit" : "Preview" }}
        </button>
      </div>
      <div id="textedit-filters">
        <select v-model="selectedNote.filter">
          <option value="All notes">All notes</option>
          <option
            v-for="notebook in notebooks"
            :key="notebook.id"
            :value="notebook.name"
          >
            {{ notebook.name }}
          </option>

          <option value="Trash">Trash</option>
        </select>
        <select v-model="selectedNote.status">
          <option value="Draft">Draft</option>
          <option value="Active">Active</option>
          <option value="Completed">Completed</option>
          <option value="Archived">Archived</option>
        </select>

        <select
          v-if="tags.length > 0"
          v-model="selectedTag"
          @change="updateSelectedNoteTag"
        >
          <option
            v-for="tag in tags"
            :key="tag.id"
            :value="tag.name"
            :style="`color:var(--${tag.color})`"
          >
            {{ tag.name }}
          </option>
        </select>
      </div>

      <MarkdownEditor
        :initialMarkdown="selectedNote.description"
        :isPreviewVisible="isPreviewMode"
      />
    </div>
  </div>
</template>

<script>
import {
  Trash2,
  Notebook,
  ScrollText,
  FileCheck,
  Tags,
  Tag,
  SquarePen,
  CircleAlert,
  CirclePlus,
  Menu,
} from "lucide-vue-next";
import MarkdownEditor from "./components/MarkdownEditor.vue";
export default {
  name: "App",
  components: {
    Trash2,
    Notebook,
    ScrollText,
    FileCheck,
    Tags,
    Tag,
    SquarePen,
    CircleAlert,
    CirclePlus,
    MarkdownEditor,
    Menu,
  },
  data() {
    return {
      isPreviewMode: false,
      filterNotes: "All notes",
      notes: [],
      selectedNote: {
        tag: {
          id: "",
          name: "",
          color: "",
        },
      },
      noteIsSelected: false,
      tags: [],
      notebooks: [],
      selectedTag: null,
      colors: ["red", "green", "yellow", "blue", "violet", "brain", "violet"],
      noteCounters: {},
      searchNoteName: "",
      menuOpen: false,
      windowWidth: window.innerWidth,
    };
  },
  mounted() {
    window.addEventListener("resize", this.updateWindowWidth);
  },
  methods: {
    updateWindowWidth() {
      this.windowWidth = window.innerWidth;
    },
    selectNote(note) {
      this.selectedNote = note;
      this.noteIsSelected = true;
    },
    newNote() {
      let note = {
        name: "New note",
        tag: {},
        status: "Draft",
        description: "",
        filter: "All notes",
      };
      this.notes.push(note);
    },
    filteredNotesCount(filter) {
      return this.notes.filter(
        (note) =>
          note.status === filter ||
          note.tag.name === filter ||
          note.filter === filter
      ).length;
    },

    newTag() {
      let randomIndex = Math.floor(Math.random() * this.colors.length);
      let randomColor = this.colors[randomIndex];
      console.log(randomColor);
      let tag = {
        name: this.generateIncrementedNoteName("New tag"),
        color: randomColor,
      };
      this.tags.push(tag);
    },
    newNotebook() {
      let notebook = {
        name: this.generateIncrementedNoteName("New notebook"),
      };
      this.notebooks.push(notebook);
    },
    updateSelectedNoteTag() {
      const selectedTag = this.tags.find(
        (tag) => tag.name === this.selectedTag
      );

      if (this.selectedTag) {
        this.selectedNote.tag.name = selectedTag.name;
        this.selectedNote.tag.color = selectedTag.color;
      }
    },

    generateIncrementedNoteName(baseTitle) {
      if (!this.noteCounters[baseTitle]) {
        this.noteCounters[baseTitle] = 1;
      } else {
        this.noteCounters[baseTitle] += 1;
      }
      return `${baseTitle} ${this.noteCounters[baseTitle]}`;
    },
  },
  computed: {
    filteredNotes() {
      if (this.searchNoteName !== "") {
        return this.notes.filter((note) =>
          note.name.toLowerCase().includes(this.searchNoteName.toLowerCase())
        );
      }
      if (this.filterNotes === "All notes") {
        return this.notes;
      }
      return this.notes.filter(
        (note) =>
          note.status === this.filterNotes ||
          note.tag.name === this.filterNotes ||
          note.filter === this.filterNotes
      );
    },
  },
};
</script>
