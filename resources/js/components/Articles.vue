<template>
    <div>
        <h2>Articles</h2>
        <form @submit.prevent = "addArticle" class="mb-3 p-2 bg-white shadow-sm">
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Title" v-model="article.title">
            </div>
            <div class="form-group">
                <textarea class="form-control" placeholder="Body" v-model="article.body">
                </textarea>
            </div>
            <button type="submit" class="btn btn-primary btn-block">Save</button>
        </form>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{ disabled: !pagination.prev_page_url }]" class="page-item">
                    <a class="page-link" href="#"
                        @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
                
                <li class="page-item disabled">
                    <a class="page-link text-dark" href="#">
                        Page {{ pagination.current_page }} of {{ pagination.last_page }}</a></li>

                <li v-bind:class="[{ disabled: !pagination.next_page_url }]" class="page-item">
                    <a class="page-link" href="#"
                        @click="fetchArticles(pagination.next_page_url)">Next</a></li>
            </ul>
        </nav>        
        <div class="card card-body border-0 mb-2 shadow-sm" v-for="article in articles" v-bind:key="article.id">
            <h3>{{ article.title }}</h3>
            <p>{{ article.body }}</p>

            <hr>
            <div class="d-flex w-100">
                <button @click="editArticle(article)" class="btn btn-warning w-50 mr-1">Edit</button>
                <button @click="deleteArticle(article.id)" class="btn btn-danger w-50 ml-1">Delete</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            articles:[],
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
    created(){
        this.fetchArticles();
    },
    methods: {
        fetchArticles(page_url){
            let vn = this
            page_url = page_url || '/api/articles'
            fetch(page_url)
                .then(res => res.json())
                .then(res => {
                    this.articles = res.data;
                    vn.makePagination(res.meta, res.links);
                })
                .catch(err => console.log(err))
        },
        makePagination(meta, links){
            let pagination = {
                current_page: meta.current_page,
                last_page: meta.last_page,
                prev_page_url: links.prev,
                next_page_url: links.next
            };

            this.pagination = pagination
        },
        deleteArticle(id){
            if(confirm('Are you sure bro ?')){
                fetch(`api/article/${id}`, {
                    method: 'delete'
                })
                .then(res => res.json())
                .then(data => {
                    alert('Article Removed')
                    this.fetchArticles();
                })
                .catch(err => console.log(err))
            }
        },
        addArticle(){
            if(this.edit === false){
                fetch('/api/article', {
                    method: 'post',
                    body: JSON.stringify(this.article),
                    headers: {
                        'content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.article.title = '';
                    this.article.body = '';
                    alert('Article Added');
                    this.fetchArticles();
                })
                .catch(err => console.log(log(err)))
            }else{
                fetch('/api/article', {
                    method: 'put',
                    body: JSON.stringify(this.article),
                    headers: {
                        'content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.article.title = '';
                    this.article.body = '';
                    alert('Article Updated');
                    this.fetchArticles();
                })
                .catch(err => console.log(log(err)))
            }
        },
        editArticle(article){
            this.edit = true;
            this.article.id = article.id;
            this.article.article_id = article.id;
            this.article.title = article.title;
            this.article.body = article.body;
        }
    }
}
</script>