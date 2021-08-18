<template>
    <v-app>
        <v-overlay z-index="7" :value="apiProcessing">
            <v-progress-circular indeterminate size="64"></v-progress-circular>
        </v-overlay>
        <v-navigation-drawer
            class="navbar"
            app
            left
            permanent
            v-if="isLoggedIn"
            :expand-on-hover="$vuetify.breakpoint.width < 500"
        >
            <v-img
                class="ml-6 mt-10 mb-8"
                src="/img/logo.svg"
                width="60px"
            ></v-img>
            <v-list nav>
                <v-list-item color="#8d8d8d" dense link to="/mycompany">
                    <v-list-item-avatar>
                        <v-icon>mdi-account-supervisor</v-icon>
                    </v-list-item-avatar>
                    <v-list-item-content>
                        <v-list-item-title>Моя компания</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
                <v-list-item color="#8d8d8d" dense link to="/catalogue">
                    <v-list-item-avatar>
                        <v-icon>mdi-view-dashboard</v-icon>
                    </v-list-item-avatar>
                    <v-list-item-content>
                        <v-list-item-title>Каталог</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
                <v-list-item color="#8d8d8d" dense link to="/orders">
                    <v-list-item-avatar>
                        <v-icon>mdi-text-box-multiple</v-icon>
                    </v-list-item-avatar>
                    <v-list-item-content>
                        <v-list-item-title>Заказы</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
            </v-list>
            <div class="bottom ml-2">
                <v-btn class="btn-logout" text @click="logout">
                    <img src="/img/back.svg" alt="logout" />
                    <span class="text">Выйти</span>
                </v-btn>
            </div>
        </v-navigation-drawer>
        <router-view></router-view>
        <v-snackbar
            v-model="snackbar"
            timeout="4000"
            >
            {{alertText}}
            <template v-slot:action="{ attrs }">
                <v-btn
                color="blue"
                text
                v-bind="attrs"
                @click="snackbar = false"
                >
                Close
                </v-btn>
            </template>
        </v-snackbar>
    </v-app>
</template>

<script>
    //Set your APP_ID
      var APP_ID = "nzg6ms9x";

      window.intercomSettings = {
        app_id: APP_ID,
      };
      (function () {
        var w = window;
        var ic = w.Intercom;
        if (typeof ic === "function") {
          ic("reattach_activator");
          ic("update", w.intercomSettings);
        } else {
          var d = document;
          var i = function () {
            i.c(arguments);
          };
          i.q = [];
          i.c = function (args) {
            i.q.push(args);
          };
          w.Intercom = i;
          var l = function () {
            var s = d.createElement("script");
            s.type = "text/javascript";
            s.async = true;
            s.src = "https://widget.intercom.io/widget/" + APP_ID;
            var x = d.getElementsByTagName("script")[0];
            x.parentNode.insertBefore(s, x);
          };
          if (document.readyState === "complete") {
            l();
          } else if (w.attachEvent) {
            w.attachEvent("onload", l);
          } else {
            w.addEventListener("load", l, false);
          }
        }
      })();
export default {
    computed: {
        isLoggedIn() {
            return this.$store.getters.isLoggedIn;
        },
    },
    data() {
        return {
          apiProcessing: false,
          snackbar: false,
          alertText: null
        };
    },
    methods: {
        async logout() {
            await this.$store.dispatch("logout");
            this.$router.push("/");
        },
    },
    created: function () {
        this.apiProcessing = this.$store.state.apiProcessing
        const context = this;
        this.$http.interceptors.response.use(undefined, async function (err) {
            if (err.response.status === 401 || err.response.status === 403) {
                await context.logout();
            }
            else if (err.response.status === 422){
                context.snackbar = true
                context.alertText = err.response.data.message
            }
        });
    },
    watch: {
      "$store.state.apiProcessing": function () {
        this.apiProcessing = this.$store.state.apiProcessing
      },
      "$store.state.alert": function () {
        this.snackbar = this.$store.state.alert.show
        this.alertText = this.$store.state.alert.text
      }
    },
};
</script>

<style>
.navbar .v-list--nav {
    padding: 0px;
}
.navbar .v-list--nav .v-list-item {
    margin: 0px !important;
}
.navbar .v-list-item::before,
.v-ripple__container {
    border-radius: 0px !important;
}
.navbar .v-list-item--active .v-avatar {
    color: #ff2d34;
}
.navbar .v-list-item--active .v-list-item__title {
    color: #ff2d34;
}
.navbar .bottom {
    background: url(/img/navbar-bottom.svg);
    background-repeat: no-repeat;
    height: 100px;
    position: absolute;
    bottom: 0px;
    width: 100%;
}
.navbar .bottom .btn-logout {
    position: absolute;
    top: 50%;
    left: 10px;
    transform: translate(0%, -50%);
    padding: 0 10px !important;
}
.navbar .bottom .btn-logout .text {
    text-transform: none;
    margin-left: 10px;
}
.bold{
    font-weight: 700 !important;
}
.thin{
    font-weight: 300 !important;
}
.center{
    text-align: center;
}
.left{
    text-align: left;
}
.right{
    text-align: right;
}
.absolute{
    position:absolute;
}
.relative{
    position: relative;
}
.row{
    margin-right: -12px;
    margin-left: -12px;
    margin-bottom: 0px !important;
    margin-top: 0px !important;
}
.no-underline{
    text-decoration: none;
    color: inherit !important;
}
.underline{
    text-decoration: underline;
}
</style>
