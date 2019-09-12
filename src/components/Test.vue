<template>
    <div id="test">
        <span v-bind:title="message">
            Hover your mouse over me for a few seconds
            to see my dynamically bound title!
        </span>
    </div>
</template>

<script>
export default {
    name: "Test",
    data: function() {
        return {
            message: new Date().toLocaleString()
        };
    }
};
</script>

<style>
</style>