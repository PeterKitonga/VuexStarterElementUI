<template>
    <el-menu class="el-menu-custom" mode="horizontal" default-active="collapse" :router="true">
        <el-menu-item index="collapse" route="#">
            <el-button v-on:click="toggleSidebar" type="text" size="mini">
                <i :class="this.$store.state['sidebar_collapse'] === true ? 'el-icon-s-unfold' : 'el-icon-s-fold'"></i>
            </el-button>
        </el-menu-item>
        <el-menu-item index="logo" :route="homeRoute.path">VuexStarter</el-menu-item>
        <el-menu-item index="logout" route="#" style="float: right">
            <el-dropdown @command="handleCommands" trigger="click">
                <span>
                    <img :src="auth.image === null ? base_url + 'images/user-avatar.png' : auth.image" alt="User Image" class="img-circle" style="height: 35px;width: 35px;object-fit: cover;border-radius: 50%">
                    <i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item :command="{method: 'routePush', params: {name: 'users.profile'}}">Profile</el-dropdown-item>
                    <el-dropdown-item :command="{method: 'logout', params: {}}">Logout</el-dropdown-item>
                </el-dropdown-menu>
            </el-dropdown>
        </el-menu-item>
    </el-menu>
</template>

<script>
    import {INIT_LOGOUT_AUTH_USER, SET_SIDEBAR_COLLAPSE_STATUS} from "../../store/types";

    export default {
        name: "auth-header",
        data: function() {
            return {
                base_url: process.env.BASE_URL,
            }
        },
        computed: {
            auth: function () {
                return this.$store.state['auth_object'];
            },
            authRole: function () {
                return this.auth['role'];
            },
            homeRoute: function () {
                let that = this;
                let authRoutes = this.$router.options.routes[1]['children'];

                return authRoutes.find(function (element) {
                    return element.name === `${that.authRole}.home`;
                });
            }
        },
        methods: {
            toggleSidebar: function () {
                let status = this.$store.state['sidebar_collapse'] !== true;

                return this.$store.commit(SET_SIDEBAR_COLLAPSE_STATUS, status);
            },
            handleCommands: function (command) {
                this[command.method](command.params);
            },
            routePush: function (route) {
                return this.$router.push(route);
            },
            logout: function () {
                return this.$store.dispatch(INIT_LOGOUT_AUTH_USER).then(response => {
                    this.$message({
                        type: response.status,
                        showClose: true,
                        duration: 10000,
                        message: response.message,
                    });
                }).catch(error => {
                    this.$message({
                        type: 'error',
                        showClose: true,
                        duration: 10000,
                        message: error.data.message,
                    });
                })
            }
        },
        mounted: function () {

        }
    }
</script>

<style scoped>

</style>