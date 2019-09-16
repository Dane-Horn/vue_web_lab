<template>
    <v-col
        @mousedown="() => blockClicked(false)"
        @mouseover="() => blockClicked(true)"
        v-bind:class="[classArray]"
        class="block"
    ></v-col>
</template>

<script>
export default {
    name: "Block",
    data() {
        return {};
    },
    props: ["weight", "value", "pos", "dragging"],
    computed: {
        seen() {
            return this.value == -1;
        },
        visited() {
            return this.value == -2;
        },
        classArray() {
            return [this.gridClass, this.blockWeight];
        },
        classType() {
            return this.type;
        },
        gridClass() {
            if (this.weight == 7) {
                if (this.visited) return ["end", "weight-1"];
                return "end";
            }
            if (this.weight == 6) return "start";
            if (this.weight == 5) return "wall";
            if (this.visited) {
                return "visited";
            }
            if (this.seen) {
                return "seen";
            }
            return "clear";
        },
        blockWeight() {
            if (this.weight == 1) return "weight-1";
            if (this.weight == 2) return "weight-2";
            if (this.weight == 3) return "weight-3";
            if (this.weight == 4) return "weight-4";
            return "weight-0";
        }
    },
    methods: {
        blockClicked(dragAction) {
            {
                if (this.weight == 6 && !dragAction) this.$emit("get-path");
                else if (this.weight == 7 && !dragAction)
                    this.$emit("clear-path");
                else if (this.dragging || !dragAction) {
                    this.$emit("block-clicked", this.pos, this.weight);
                }
            }
        },
        blockClicked2() {
            if (this.weight == 6) this.$emit("get-path");
            else if (this.weight == 7) this.$emit("clear-path");
            else this.$emit("block-clicked", this.pos, this.weight);
        }
    }
};
</script>

<style>
.block {
    border-style: solid;
    border-color: black;
    border-width: 1px;
    width: 100px;
    height: 100px;
    vertical-align: middle;
    text-align: center;
    padding: 5px 0;
    font-size: 20px;
}
.seen {
    background-color: skyblue;
}
.clear {
    background-color: white;
}
.visited {
    background-color: darkblue;
}
.wall {
    background-color: black;
}
.start {
    background-color: darkgreen;
}
.end {
    background-color: red;
}
.weight-0 {
    filter: brightness(120%);
}
.weight-1 {
    filter: brightness(80%);
}
.weight-2 {
    filter: brightness(60%);
}
.weight-3 {
    filter: brightness(40%);
}
.weight-4 {
    filter: brightness(20%);
}
</style>