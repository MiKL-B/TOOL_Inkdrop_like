<template>
  <div
    id="textedit-preview"
    v-if="isPreviewVisible"
    v-html="getMarkdownHtml()"
  ></div>

  <textarea
    v-else
    id="textedit"
    placeholder="Enter text here..."
    v-model="markdownText"
  ></textarea>
</template>

<script>
import { marked } from "marked";
export default {
  name: "MarkdownEditor",
  props: {
    initialMarkdown: {
      type: String,
      default: "",
    },
    isPreviewVisible: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      markdownText: this.initialMarkdown,
    };
  },
  methods: {
    getMarkdownHtml() {
      return marked(this.markdownText, { sanitize: true });
    },
  },
};
</script>
<style >
/* Titres */
h1 {
  font-size: 2.5em;
  margin-bottom: 0.5em;
  color: #2c3e50;
}

h2 {
  font-size: 2em;
  margin-bottom: 0.5em;
  color: #2980b9;
}

h3 {
  font-size: 1.75em;
  margin-bottom: 0.5em;
  color: #8e44ad;
}

/* Paragraphes */
p {
  margin: 1em 0;
}



ol {
  padding-left: 20px;
}

ol li {
  margin-bottom: 0.5em;
  list-style-type: decimal;
}

/* Liens */
a {
  color: #2980b9;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

/* Images */
img {
  max-width: 100%;
  height: auto;
  display: block;
  margin: 1em 0;
}

/* Bloc de code */
pre {
  background-color: #2c3e50;
  color: #ecf0f1;
  padding: 10px;
  border-radius: 5px;
  overflow-x: auto;
}

code {
  color: #ecf0f1;
  padding: 2px 4px;
  border-radius: 4px;
}

/* Citations */
blockquote {
  margin: 1em 0;
  padding: 10px 20px;
  background-color: #e2e2e2;
  border-left: 5px solid #2980b9;
  font-style: italic;
}

/* Styles des tableaux */
table {
  width: 100%;
  border-collapse: collapse;
  margin: 1em 0;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #2980b9;
  color: white;
}
</style>
