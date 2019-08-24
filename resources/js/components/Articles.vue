<template>
  <div id="articles">

    <h1>Articles</h1>
    <!-- add form -->
    <form action="" class="mb-3" @submit.prevent="addArticle">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Title" v-model="article.title">
      </div>
      <div class="form-group">
        <textarea class="form-control" placeholder="Body" v-model="article.body"></textarea>
      </div>
      <button type="submit" class="btn btn-light btn-block">Save</button>
    </form>
    <!-- pagination -->
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li :class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
        <li class="page-item disabled"><a class="page-link" href="#">Page {{pagination.current_page}} of {{pagination.last_page}}</a></li>
        <li :class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a></li>
      </ul>
    </nav>
    <!-- articles -->
    <div v-for="article in articles" :key="article.id" class="card card-body mb-2">
      <h3>{{article.title}}</h3>
      <p>{{article.body}}</p>
      <hr>
      <div class="row">
        
      <button class="btn btn-danger btn-sm" @click="deleteArticle(article.id)">Delete</button>
      <button class="btn btn-info btn-sm mx-3" @click="editArticle(article.id)">Edit</button>
      </div>
    </div>
  </div>
</template>

<script>
import { log } from 'util';
export default {
  data() {
    return {
      articles: [],
      article: {
        id: '',
        title: '',
        body: ''
      },
      article_id: '',
      pagination: {},
      edit: false
    }
  },

  created() {
    this.fetchArticles();
  },

  methods: {
    fetchArticles(page_url) {
      let vm = this;
      page_url = page_url || 'api/v1/articles'
      fetch(page_url)
        .then(res => res.json())
        .then(res => {
          this.articles = res.data
          vm.makePagination(res.meta, res.links);
        })
        .catch(err => console.log(err))
    },
    makePagination(meta, links) {
      let pagination = {
        current_page: meta.current_page,
        last_page: meta.last_page,
        next_page_url: links.next,
        prev_page_url: links.prev
      }

      this.pagination = pagination
    },
    deleteArticle(id) {
      if (confirm('Are you sure?')) {
        fetch(`api/v1/article/${id}`, {
          method: 'delete'
        })
        .then(res => res.json)
        .then(data => {
          alert("Article Removed")
          this.fetchArticles();
        })
        .catch(err => console.log(err))
      }
    },
    addArticle() {
      if(this.edit === false) {
        // Add
        fetch('api/v1/article', {
          method: 'post',
          body: JSON.stringify(this.article),
          headers: {
            'content-type': 'application/json'
          }
        })
        .then(res => res.json())
        .then(data => {
          this.article.title = ''
          this.article.body = ''
          alert('Article Added')
          this.fetchArticles()
        })
        .catch(err => console.log(err)
        )
      } else {
        // Update
        fetch(`api/v1/article/${id}`, {
          method: 'put',
          body: JSON.stringify(this.article),
          headers: {
            'content-type': 'application/json'
          }
        })
        .then(res => res.json())
        .then(data => {
          this.article.title = ''
          this.article.body = ''
          alert('Article Updated')
          this.fetchArticles()
        })
        .catch(err => console.log(err)
        )

      }
    },
    editArticle(article) {
      // console.log(article)
      this.edit = true
      this.article.id =article.id
      this.article.article_id = article.id
      this.article.title = article.title
      this.article.body = article.body
    }
  }
}
</script>

<style scoped>

</style>