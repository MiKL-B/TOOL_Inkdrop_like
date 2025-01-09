<template>
  <div id="columns">
    <!-- menu -->
    <div id="menu">
      <aside>
        <ul class="menu-list">
          <li class="flex justify-between" @click="filterNotes = 'All notes'">
            <div class="flex gap-4">
              <ScrollText width="16" height="16" />
              <a>All notes</a>
            </div>
            <span class="menu-item-count">{{ notes.length }}</span>
          </li>
          <!-- notebooks -->
          <details>
            <summary>
              <Notebook width="16" height="16" />
              <span>Notebooks</span>
            </summary>
            <ul>
              <li class="flex justify-between pl-8">
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Ideas</a>
                </div>
                <span class="menu-item-count">0</span>
              </li>
              <li class="flex justify-between pl-8">
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Hobby</a>
                </div>
                <span class="menu-item-count">0</span>
              </li>
              <li class="flex justify-between pl-8">
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Learn</a>
                </div>
                <span class="menu-item-count">0</span>
              </li>
              <li class="flex justify-between pl-8">
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Tips</a>
                </div>
                <span class="menu-item-count">0</span>
              </li>
              <li class="flex justify-between pl-8">
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Website</a>
                </div>
                <span class="menu-item-count">0</span>
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
                class="flex justify-between pl-8"
                @click="filterNotes = 'Draft'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Draft</a>
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount("Draft")
                }}</span>
              </li>
              <li
                class="flex justify-between pl-8"
                @click="filterNotes = 'Active'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Active</a>
                </div>
                <span class="menu-item-count">
                  {{ filteredNotesCount("Active") }}
                </span>
              </li>
              <li
                class="flex justify-between pl-8"
                @click="filterNotes = 'Completed'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Completed</a>
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount("Completed")
                }}</span>
              </li>
              <li
                class="flex justify-between pl-8"
                @click="filterNotes = 'Archived'"
              >
                <div class="flex gap-4">
                  <SquarePen width="16" height="16" />
                  <a>Archived</a>
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount("Archived")
                }}</span>
              </li>
            </ul>
          </details>
          <!-- tags -->
          <li class="flex gap-4" @click="newTag">
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
                class="flex justify-between pl-8"
                v-for="tag in tags"
                :key="tag.id"
                @click="filterNotes = tag.name"
              >
                <div class="flex gap-4">
                  <Tag width="16" height="16" :color="`var(--${tag.color})`" />
                  <input id="input-tag" type="text" v-model="tag.name" />
                </div>
                <span class="menu-item-count">{{
                  filteredNotesCount(tag.name)
                }}</span>
              </li>
            </ul>
          </details>

          <li class="flex justify-between">
            <div class="flex gap-4">
              <Trash2 width="16" height="16" />
              <a>Trash</a>
            </div>
            <span class="menu-item-count">0</span>
          </li>
        </ul>
      </aside>
      <div id="settings">
        <figure class="image is-32x32">
          <img
            class="is-rounded"
            src="https://bulma.io/assets/images/placeholders/128x128.png"
          />
        </figure>
        <div
          id="settings-info"
          class="is-flex is-flex-direction-column has-text-light"
        >
          <span>Name</span>
          <span class="is-small">Email</span>
        </div>
      </div>
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
        <input id="search-notes" type="text" placeholder="Search notes..." />
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
          <p>{{ note.status }}</p>
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
        <!-- <select name="" id="">
          <option value="">All notes</option>
          <option value="">Pinned notes</option>
          <option value="">Ideas</option>
          <option value="">Hobby</option>
          <option value="">Learn</option>
          <option value="">Tips</option>
          <option value="">Website</option>
          <option value="">Trash</option>
        </select> -->
        <select v-model="selectedNote.status">
          <option value="All notes">No status</option>
          <option value="Draft">Draft</option>
          <option value="Active">Active</option>
          <option value="Completed">Completed</option>
          <option value="Archived">Archived</option>
        </select>
        <!-- <span :class="selectedNote.tag ? 'tag' : ''">{{
          selectedNote.tag
        }}</span>

        <input id="textedit-addtags" type="text" placeholder="Add Tags" /> -->
        <select v-model="selectedTag" @change="updateSelectedNoteTag">
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
      <div
        id="textedit-preview"
        v-if="isPreviewMode"
        v-html="getMarkdownHtml()"
      ></div>
      <textarea
        v-else
        id="textedit"
        placeholder="Enter text here..."
        v-model="selectedNote.description"
      ></textarea>
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
} from "lucide-vue-next";
import { marked } from "marked";
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
      selectedTag: null,
      colors: ["red", "green", "yellow", "blue", "violet", "brain", "violet"],
      noteCounters: {},
    };
  },
  methods: {
    getMarkdownHtml() {
      return marked(this.selectedNote.description, { sanitize: true });
    },
    selectNote(note) {
      this.selectedNote = note;
      this.noteIsSelected = true;
    },
    newNote() {
      let note = {
        name: "New note",
        tag: {},
        status: "",
        description: "",
      };
      this.notes.push(note);
    },
    filteredNotesCount(filter) {
      return this.notes.filter(
        (note) => note.status === filter || note.tag.name === filter
      ).length;
    },

    newTag() {
      let randomIndex = Math.floor(Math.random() * this.colors.length);
      let randomColor = this.colors[randomIndex];
      console.log(randomColor);
      let tag = {
        name: this.generateIncrementedNoteName('New note'),
        color: randomColor,
      };
      this.tags.push(tag);
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
      // Vérifier si le titre de base existe déjà dans l'objet
      if (!this.noteCounters[baseTitle]) {
        this.noteCounters[baseTitle] = 1; // Initialiser le compteur
      } else {
        this.noteCounters[baseTitle] += 1; // Incrémenter le compteur
      }

      // Retourner le nom de note avec le compteur
      return `${baseTitle} ${this.noteCounters[baseTitle]}`;
    },
  },
  computed: {
    filteredNotes() {
      if (this.filterNotes === "All notes") {
        return this.notes;
      }
      return this.notes.filter(
        (note) =>
          note.status === this.filterNotes || note.tag.name === this.filterNotes
      );
    },
  },
};
</script>
