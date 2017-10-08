<template>
       <v-app id="app" standalone>
        <v-toolbar fixed>
             <v-container>
            <v-toolbar-title class="orange--text text-xs-center">Streaming Speech Recognition</v-toolbar-title>
            </v-container>
        </v-toolbar>
        <main>
            <v-container>
               <router-view></router-view>
            </v-container>
        </main>
    </v-app>
</template>

<script>
export default {

}
</script>


