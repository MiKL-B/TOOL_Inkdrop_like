<template>
  <div id="columns">
    <!-- menu -->
    <div id="menu">
      <aside>
        <ul class="menu-list">
          <li>
            <a>All notes</a>
          </li>
          <li>
            <a>Pinned notes</a>
          </li>
          <li>
            <a>Notebooks</a>
          </li>

          <ul class="sub-menu-list">
            <li><a>Ideas</a></li>
            <li><a>Hobby</a></li>
            <li><a>Learn</a></li>
            <li><a>Tips</a></li>
            <li><a>Website</a></li>
          </ul>
          <li>
            <a>Status</a>
          </li>
          <ul class="sub-menu-list">
            <li><a>Draft</a></li>
            <li><a>Active</a></li>
            <li><a>Completed</a></li>
            <li><a>Archived</a></li>
          </ul>
          <li>
            <a>Trash</a>
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
          <p>All notes</p>
          <button @click="newNote">New note</button>
        </div>
        <input id="search-notes" type="text" placeholder="Search notes..." />
      </div>

      <div id="notes">
        <div
          @click="selectNote(note)"
          class="note"
          v-for="note in notes"
          :key="note"
        >
          <h2>{{ note.name }}</h2>
          <span class="tag">{{ note.tag }}</span>
          <p class="note-description">
            {{ note.description }}
          </p>
        </div>
      </div>
    </div>
    <!-- textedit -->
    <div id="textedit-container" v-if="noteIsSelected">
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
        <select name="" id="">
          <option value="">All notes</option>
          <option value="">Pinned notes</option>
          <option value="">Ideas</option>
          <option value="">Hobby</option>
          <option value="">Learn</option>
          <option value="">Tips</option>
          <option value="">Website</option>
          <option value="">Trash</option>
        </select>
        <select name="" id="">
          <option value="">Draft</option>
          <option value="">Active</option>
          <option value="">Completed</option>
          <option value="">Archived</option>
          <option value="">All notes</option>
        </select>
        <span :class="selectedNote.tag ? 'tag' : ''">{{ selectedNote.tag }}</span>
        <input id="textedit-addtags" type="text" placeholder="Add Tags">
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
import { marked } from "marked";
export default {
  name: "App",
  data() {
    return {
      isPreviewMode: false,
      notes: [],
      selectedNote: {},
      noteIsSelected:false,
    };
  },
  methods: {
    getMarkdownHtml() {
      return marked(this.selectedNote.description, { sanitize: true });
    },
    selectNote(note){
      this.selectedNote = note;
      this.noteIsSelected = true;
    },
    newNote() {
      let note = {
        name: "New note",
        tag: "new",
        description: "",
      };
      this.notes.push(note);
    },

  },
};
</script>
