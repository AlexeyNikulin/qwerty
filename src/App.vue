<template>
    <div class="app">
        <h1>Страница с постами</h1>
        <my-input
            v-model="searchQuery"
            placeholder="Поиск..."
        />
        <div class="app__btns">
            <my-buttons @click="showDialog">
                Создать пользователя
            </my-buttons>
            <my-select 
                v-model="selectedSord"
                :options="sortOptions"
            />
        </div>
        <my-dialog v-model:show="dialogVisible">
            <post-form 
                @create="createPost"/>
        </my-dialog>
        <post-list 
            :posts="sortedAndSearchedPosts"
            @remove="removePost"
            v-if="!isPostsLoading"/>
        <div v-else>Идет загрузка...</div>
        <div ref="observer" class="observer"></div>
        <!-- <div class="page__wrapper">
            <div 
                v-for="pageNumber in totalPages" 
                :key="pageNumber"
                class="page"
                :class="{
                    'current-page': pageNumber === page
                }"
                @click="changePage(pageNumber)"
            >{{ pageNumber }}</div>
        </div> -->
    </div>
</template>

<script>
import PostForm from './componets/PostForm.vue';
import PostList from '@/componets/PostList.vue';
import MyButtons from './componets/UI/MyButtons.vue';
import axios from 'axios';

export default {
    components: {
        PostForm, PostList,
        MyButtons
    },  
    data() {
        return {
            posts: [],
            dialogVisible: false,
            isPostsLoading: false,
            selectedSord: '',
            searchQuery: '',
            page: 1,
            limit: 10,
            totalPages: 0,
            sortOptions: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По содержанию'},
            ]
        }
    },
    methods: {
        createPost(post) {
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id);
        },
        showDialog() {
            this.dialogVisible = true;
        },
        // changePage(pageNumber) {
        //     this.page = pageNumber;
        // },  
        async fetchPosts() {
            try{
                this.isPostsLoading = true;
                const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _page: this.page,
                        _limit: this.limit,
                    }
                });
                this.totalPages = Math.ceil(res.headers['x-total-count'] / this.limit);
                this.posts = res.data;
                this.isPostsLoading = false;
            } catch{
                alert('ошибка');
            }
        },
        async loadMorePosts() {
            try{
                this.isPostsLoading = true;
                const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _page: this.page,
                        _limit: this.limit,
                    }
                });
                this.totalPages = Math.ceil(res.headers['x-total-count'] / this.limit);
                this.posts = [...this.posts, ...res.data];
                this.isPostsLoading = false;
            } catch{
                alert('ошибка');
            }
        }
    },
    mounted() {
        this.fetchPosts();
        console.log(this.$refs.observer);
        const options = {
            rootMargin: '0px',
            threshold: 1.0
        }
        const callback = function(entries, onserver) {

        }
        const observer = new IntersectionObserver(callback, options);
        observer.observe(this.$refs.observer);
    },
    computed: {
        sortedPosts() {
            return [...this.posts].sort((post1, post2) => post1[this.selectedSord]?.localeCompare(post2[this.selectedSord]));
        },
        sortedAndSearchedPosts() {
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()));
        },
    },
    watch: {
        // page() {
        //     this.fetchPosts();
        // }
    }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.app {
    padding: 20px;
}

.app__btns {
    display: flex;
    justify-content: space-between;
    margin: 15px 0;
}

.page__wrapper {
    display: flex;
    justify-content: center;
    margin-top: 15px;
}

.page {
    border: 1px solid black;
    padding: 10px;
    margin: 0 5px;
    cursor: pointer;
}

.current-page {
    border: 4px solid teal;
}

.observer {
    height: 30px;
    background-color: yellow;
}
</style>