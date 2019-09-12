<template>
    <div v-bind:class="[gridClass(classType)]" class="block">{{weight}}</div>
</template>

<script>
export default {
    name: "Block",
    data() {
        return {
            prev: 0
        };
    },
    props: ["type", "weight"],
    methods: {
        gridClass(i) {
            switch (i) {
                case 1:
                    return "seen";
                case 2:
                    return "visited";
                default:
                    return "clear";
            }
        }
    },
    computed: {
        classType() {
            if (this.prev == 0 || (this.prev == 1 && this.type == 2)) {
                this.prev = this.type;
                return this.type;
            } else {
                return this.prev;
            }
        }
    }
};
</script>

<style>
.block {
    border-style: solid;
    width: 100px;
    height: 100px;
    vertical-align: middle;
    text-align: center;
    padding: 5px 0;
    font-size: 20px;
}
.seen {
    background-color: lightblue;
}
.clear {
    background-color: white;
}
.visited {
    background-color: blueviolet;
}
</style>