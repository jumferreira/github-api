<template>
    <div>
        <div class="header">
            <div class="container">
                <div class="columns items-center">
                    <div class="column column--2">
                        <h1 class="header__logo">
                            <a href="https://www.astrolink.com.br/">
                                <img class="header__image" src="https://pbs.twimg.com/profile_images/803640243106758656/rFsTi4_K_400x400.jpg" alt="">
                            </a>
                        </h1>
                    </div>

                    <div class="column">
                        <div class="header__search">
                            <input
                                type="text"
                                class="input input--grey-darker input--full"
                                placeholder="Busca de usuários"
                                v-model="searchField"
                                @keyup="search()"
                            >

                            <ul class="header__search-results" v-if="hasResults">
                                <li
                                    class="header__search-result"
                                    v-for="item in searchResult"
                                >
                                    <p>Nome: {{ item.name }}</p>
                                    <p>Usuário: {{ item.username }}</p>
                                    <p>E-mail: {{ item.email }}</p>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <hr>

        <div class="body">
            <div class="container">
                <div class="columns">
                    <div class="column">
                        <h2 class="users_list__title">Resultado da busca: </h2>

                        <div class="users_list">

                            <div class="users_list__item" v-for="user in usersResults">
                                <div class="users_list__item__image">
                                    <img :src="user.avatar_url" :alt="user.login">
                                </div>

                                <div class="users_list__item__info">
                                    <ul>
                                        <li>
                                            <span class="font-semibold">User: </span>
                                            <span v-text="user.login"></span>
                                        </li>

                                        <li>
                                            <span class="font-semibold">
                                                Followers ({{user.followers}}):
                                            </span>

                                            <ul class="users_list__item__followers">
                                                <li v-for="follower in followersList">
                                                    <a
                                                        :href="follower.html_url"
                                                        target="_blank"
                                                        v-text="follower.login"
                                                    ></a>
                                                </li>
                                            </ul>
                                        </li>

                                        <li>
                                            <span class="font-semibold">
                                                Following ({{user.following}}):
                                            </span>

                                            <ul class="users_list__item__following">
                                                <li v-for="following in followingList">
                                                    <a
                                                        :href="following.html_url"
                                                        target="_blank"
                                                        v-text="following.login"
                                                    ></a>
                                                </li>
                                            </ul>
                                        </li>

                                        <li>
                                            <span class="font-semibold">E-mail: </span>
                                            <span v-text="user.email !== null ? user.email : '(E-mail privado)'"></span>
                                        </li>

                                        <li>
                                            <span class="font-semibold">Bio: </span>
                                            <span v-text="user.bio !== null ? user.bio : '(Bio não fornecida)'"></span>
                                        </li>
                                    </ul>
                                </div>
                            </div>

                            <div class="users_list__repository">
                                <div class="users_list__repository__head">
                                    <h3 class="">Repositórios</h3>

                                    <button @click="orderRepositories()">
                                        <i :class="`fas fa-arrow-${isArrowClicked ? 'up' : 'down'}`"></i>
                                    </button>
                                </div>

                                <ul class="users_list__repository__items">
                                    <li
                                        class="users_list__repository__item"
                                        v-for="repository in repositoriesList"
                                    >
                                        <span v-text="repository.name"></span>

                                        <span v-text="repository.stargazers_count + ' star'"></span>
                                    </li>
                                </ul>
                            </div>
                        </div>
                    </div>

                    <!-- <div class="column">
                        <div class="detail">
                            <h3 class="detail__title">Repositórios</h3>

                            <div v-if="isUserSelected">
                                <strong>Nome: </strong> {{ detail.name }} <br />
                                <strong>Usuário: </strong> {{ detail.username }} <br />
                                <strong>E-mail: </strong> {{ detail.email }} <br />
                                <strong>Telefone: </strong> {{ detail.phone }} <br />
                                <strong>Empresa: </strong> {{ detail.company.name }} - {{ detail.company.catchPhrase }} <br />

                            </div>

                            <div v-if="isArticleSelected">
                                <strong>Titulo: </strong> {{ detail.title }} <br />
                                <strong>Autor: </strong> {{ getAuthor(detail.userId) }} <br />
                                <strong>Texto: </strong> {{ detail.body }} <br />
                            </div>

                        </div>
                    </div> -->
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    data: () => ({
        searchField: null,
        searchResult: [],
        list: [],
        detail: [],
        articlesList: [],
        isUserSelected: false,
        isArticleSelected: false,
        hasResults: false,


        followersCount: 0,
        followingCount: 0,
        // followersList: null,
        // followingList: null,
        // usersResults: null,
        // repositoriesList: null,
        // repositoriesListAsc: null,
        // repositoriesListDesc: null,
        isArrowClicked: true,

        followersList: [
            {
                'html_url': 'https://github.com/robonilha',
                'login': 'robonilha',
            }
        ],

        followingList: [
            {
                'html_url': 'https://github.com/robonilha',
                'login': 'robonilha',
            }
        ],

        usersResults: [
            {
                'avatar_url': 'https://avatars3.githubusercontent.com/u/29998176?s=460&v=4',
                'login': 'jumferreira',
                // 'email': 'jumferreira@teste.com',
                'email': null,
                'followers': 4,
                'following': 3,
                'bio': 'Working working working',
            },
        ],

        repositoriesList: [
            {
                'name': 'jumferreira.github.io',
                'stargazers_count': 8,
            },
            {
                'name': 'newtab',
                'stargazers_count': 15,
            },
            {
                'name': 'prova',
                'stargazers_count': 0,
            },
            {
                'name': 'sparklez',
                'stargazers_count': 2,
            },

        ],
    }),

    computed: {
        repositoriesListAsc () {
            return _.orderBy(this.repositoriesList, ['stargazers_count'], ['asc']);
        },

        repositoriesListDesc () {
            return _.orderBy(this.repositoriesList, ['stargazers_count'], ['desc']);
        },
    },

    methods: {
        search: _.throttle(function () {
            this.usersList.forEach(user => {

                if (Object.values(user).indexOf(_.upperFirst(this.searchField)) > -1) {
                    this.searchResult.push(user);
                    this.hasResults = true;
                }
            });

            if(! this.searchField) {
                this.hasResults = false;
            }

        }, 500),

        async getUser () {
            // let { data } = await axios.get('https://api.github.com/search/users?q=user:brurubio');
            // let { data } = await axios.get('https://api.github.com/users/brurubio');
            // this.usersResults = data.items;
            this.getFollowers();
            this.getFollowing();
            this.getRepositories();
            this.repositoriesList = this.repositoriesListAsc;
            // console.log(data);
        },

        async getFollowers () {
            // let { data } = await axios.get('https://api.github.com/users/brurubio/followers');
            // this.followersList = data;
            // this.followersCount = data.length;
        },

        async getFollowing () {
            // let { data } = await axios.get('https://api.github.com/users/brurubio/following');
            // this.followingList = data;
            // this.followingCount = data.length;
        },

        async getRepositories () {
            // let { data } = await axios.get('https://api.github.com/users/jumferreira/repos');
            // this.repositoriesList = data;
            // console.log(data);
        },

        orderRepositories () {
            this.isArrowClicked = ! this.isArrowClicked;

            this.repositoriesList = this.isArrowClicked ? this.repositoriesListAsc : this.repositoriesListDesc;
        },
    },

    mounted() {
        this.getUser();
        // this.getArticles();
    },
};
</script>
