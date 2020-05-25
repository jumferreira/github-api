<template>
    <div class="main">
        <div class="header">
            <div class="container">
                <div class="columns items-center">
                    <div class="column logo_column">
                        <h1 class="header__logo">
                            <a href="#">
                                <img class="header__image" src="https://miro.medium.com/max/4000/0*MZMl76wKo2FQLqG0.png" alt="">
                            </a>
                        </h1>
                    </div>

                    <div class="column">
                        <div class="header__search">
                            <div class="header__search-holder">

                                <input
                                    type="text"
                                    class="input input--grey-darker input--75"
                                    placeholder="Busca de usuários"
                                    v-model="searchField"
                                    @keyup="search()"
                                >

                                <i
                                    v-if="isClearSearchActive"
                                    class="fas fa-times"
                                    @click="clearSearch()"
                                ></i>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="body">
            <div class="container">

                <div class="loader" v-if="isLoading"></div>

                <div class="columns" v-if="!isLoading">
                    <div class="column">
                        <h2
                            v-if="!isLoading && !isShowingInitialMessage"
                            class="users_list__title"
                            v-text="resultTitle"
                        ></h2>

                        <div class="empty-message" v-if="isShowingInitialMessage && isEmptyResult">
                            <h3>Bem vindo a busca de usuários do Github <i class="fab fa-github"></i></h3>

                            <p>Para usar o buscador é muito simples!</p>
                            <br />

                            <p>É só procurar pelo nome de usuário que deseja e os resultados vão aparecer bem aqui. Viu como é simples?</p>
                            <p>Ah, e caso queira saber mais sobre alguma informação na tela, procure pelas palavras sublinhadas ;)</p>
                        </div>

                        <div v-else class="users_list">
                            <div class="users_list__item">
                                <div class="users_list__item__image">
                                    <img :src="userResult.avatar_url" :alt="userResult.login">
                                </div>

                                <div class="users_list__item__info">
                                    <ul>
                                        <li>
                                            <span class="font-semibold">User: </span>
                                            <span v-text="userResult.login"></span>
                                        </li>

                                        <li>
                                            <span class="font-semibold">
                                                Followers ({{userResult.followers}}):
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
                                                Following ({{userResult.following}}):
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
                                            <span v-text="userResult.email ? userResult.email : '(E-mail privado)'"></span>
                                        </li>

                                        <li>
                                            <span class="font-semibold">Bio: </span>
                                            <span v-text="userResult.bio ? userResult.bio : '(Bio não fornecida)'"></span>
                                        </li>
                                    </ul>
                                </div>
                            </div>

                            <div class="users_list__repository">
                                <div class="users_list__repository__head">
                                    <h3>Repositórios</h3>

                                    <button @click="orderRepositories()">
                                        <i :class="`fas fa-arrow-${isArrowClicked ? 'up' : 'down'}`"></i>
                                    </button>
                                </div>

                                <ul class="users_list__repository__items">
                                    <li
                                        class="users_list__repository__item"
                                        v-for="repository in repositoriesList"
                                    >
                                        <button
                                            @click="openRepositoryModal(userResult.login, repository.name)"
                                            v-text="repository.name"
                                        ></button>

                                        <span v-text="repository.stargazers_count + ' estrela(s)'"></span>
                                    </li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <modal
            name="repositoryModal"
            :width="320"
            :height="220"
        >
            <div class="modal">
                <div class="modal_header">
                    <h1 class="modal_title" v-text="'Detalhes do repositório: ' + selectedRepository.name"></h1>

                    <button class="modal_close" @click="closeRepositoryModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>

                <div class="modal_body" v-if="isRepositorySelected">
                    <ul>
                        <li>
                            <span class="font-semibold">Nome: </span>
                            <span v-text="selectedRepository.name"></span>
                        </li>

                        <li>
                            <span class="font-semibold">Descrição: </span>
                            <span v-text="selectedRepository.description ? selectedRepository.description : '(Descrição não fornecida)'"></span>
                        </li>

                        <li>
                            <span class="font-semibold">Estrelas: </span>
                            <span v-text="selectedRepository.stargazers_count + ' estrela(s)'"></span>
                        </li>

                        <li>
                            <span class="font-semibold">Linguagem: </span>
                            <span v-text="selectedRepository.language"></span>
                        </li>

                        <li>
                            <br />
                            Para mais detalhes,
                            <a
                                class="underline"
                                :href="selectedRepository.svn_url"
                                target="_blank"
                            >
                                clique aqui
                            </a>
                        </li>
                    </ul>
                </div>
            </div>
        </modal>
    </div>
</template>

<script>

export default {
    data: () => ({
        searchField: null,

        followersList: {},
        followingList: {},
        repositoriesList: {},
        selectedRepository: {},
        userResult: {},

        followersCount: 0,
        followingCount: 0,
        isArrowClicked: true,
        isRepositorySelected: false,
        isClearSearchActive: false,
        isLoading: false,
        isShowingInitialMessage: false,
    }),

    computed: {
        repositoriesListAsc () {
            return _.orderBy(this.repositoriesList, ['stargazers_count'], ['asc']);
        },

        repositoriesListDesc () {
            return _.orderBy(this.repositoriesList, ['stargazers_count'], ['desc']);
        },

        isEmptyResult () {
            return _.isEmpty(this.userResult);
        },

        resultTitle () {
            return this.isEmptyResult ? 'Nenhum resultado encontrado. Por favor, tente outro usuário ;)' : 'Resultado da busca:';
        },
    },

    methods: {
        search: _.throttle(function () {
                this.isLoading = true;
                this.isClearSearchActive = true;
                this.isShowingInitialMessage = false;

            if (this.searchField.length > 4) {
                this.isLoading = false;
                this.getUser(this.searchField);

            } else {
                this.isClearSearchActive = false;

            }
        }, 500),

        async getUser (userName) {
            let { data } = await axios.get(`https://api.github.com/users/${userName}`);
            this.userResult = _.clone(data);

            this.getFollowers(userName);
            this.getFollowing(userName);
            this.getRepositories(userName);

            this.repositoriesList = this.repositoriesListAsc;
        },

        async getFollowers (userName) {
            let { data } = await axios.get(`https://api.github.com/users/${userName}/followers`);
            this.followersList = data;
            this.followersCount = data.length;
        },

        async getFollowing (userName) {
            let { data } = await axios.get(`https://api.github.com/users/${userName}/following`);
            this.followingList = data;
            this.followingCount = data.length;
        },

        async getRepositories (userName) {
            let { data } = await axios.get(`https://api.github.com/users/${userName}/repos`);
            this.repositoriesList = data;
        },

        async getSelectedRepository (userName, repositoryName) {
            let { data } = await axios.get(`https://api.github.com/repos/${userName}/${repositoryName}`);
            this.selectedRepository = data;
            this.isRepositorySelected = true;
        },

        openRepositoryModal (userName, repositoryName) {
            this.$modal.show('repositoryModal');

            this.getSelectedRepository(userName, repositoryName);
        },

        closeRepositoryModal () {
            this.$modal.hide('repositoryModal');
        },

        clearSearch () {
            this.searchField = null;
            this.userResult = {};
            this.isClearSearchActive = false;
            this.isShowingInitialMessage = true;

            this.followersList = {};
            this.followingList = {};
            this.repositoriesList = {};
            this.selectedRepository = {};
            this.userResult = {};
        },

        orderRepositories () {
            this.isArrowClicked = ! this.isArrowClicked;

            this.repositoriesList = this.isArrowClicked ? this.repositoriesListAsc : this.repositoriesListDesc;
        },
    },

    mounted() {
        this.isShowingInitialMessage = true;
    },
};
</script>
