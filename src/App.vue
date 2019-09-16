<template>
    <v-app>
        <v-content>
            <Grid></Grid>
        </v-content>
    </v-app>
</template>

<script>
import Grid from "./components/Grid";
export default {
    name: "App",
    components: {
        Grid
    },
    data: () => ({
        //
    })
};
</script>
