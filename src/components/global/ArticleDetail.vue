<template>
    <div class="article-detail">
      <h3 class="title">{{title}}</h3>
      <article class="content markdown-body" v-html="content">
      </article>
      <div class="manage" v-show="isAdmin">
        <span><a @click="edit">编辑</a></span>
        <span><a @click="del">删除</a></span>
      </div>
    </div>
</template>
<script>
import marked from 'marked'
import highlight from 'highlight.js'
import cookies from 'js-cookie'
marked.setOptions({
  renderer: new marked.Renderer(),
  gfm: true,
  tables: true,
  breaks: false,
  pedantic: false,
  sanitize: false,
  smartLists: true,
  smartypants: false,
  highlight: function (code) {
    return require('highlight.js').highlightAuto(code).value;
  }
})
export default {
  name: 'ArticleDetail',
  data(){
    return {
      article:this.$store.state.articleDetail
    }
  },
  title() {
      return this.article.title + ' | vueblog'
  },
  computed: {
    title() {
      return this.article.title
    },
    content() {
      return marked(this.article.content)
    },
    isAdmin() {
      return (cookies.get('token') || this.$store.state.cookies.token) ? true : false
    }
  },
  methods: {
    del() {
      let id = this.$route.params.id;
      this.axios.delete(`/article?id=${id}`).then((data) => {
        this.$toasted.show(data.data.message)
        if(data.data.code === 200) this.$router.push({name:'index'})
      })
    },
    edit() {
      let id = this.$route.params.id;
      this.$router.push({ name: 'publish', params: { id: id } })
    }
  }
}
</script>
