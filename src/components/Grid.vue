<template>
    <div id="grid" @dragstart="(event) => event.preventDefault()">
        <div class="grid-container">
            <v-toolbar></v-toolbar>
            <v-container @mousedown="dragging = true">
                <v-row :key="y" v-for="(row, y) in grid">
                    <Block
                        @get-path="getPath()"
                        @clear-path="clearPath()"
                        @block-clicked="blockClicked"
                        :dragging="dragging"
                        :value="res[y][x]"
                        :weight="grid[y][x]"
                        :pos="[x, y]"
                        :key="x"
                        v-for="(col, x) in row"
                    ></Block>
                </v-row>
            </v-container>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import lodash from "lodash";
import { setTimeout } from "timers";
import Block from "./Block";
export default {
    name: "Grid",
    components: { Block },
    data: function() {
        return {
            k: 0,
            //prettier-ignore
            grid: [
                // [6, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
                // [5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 0, 0],
                // [0, 0, 0, 0, 0, 5, 0, 0, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 5, 5, 0, 5, 0, 0, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 0, 5, 0, 5, 5, 5, 5, 5, 5, 5, 5],
                // [0, 0, 0, 5, 0, 5, 0, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 0, 0, 0, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 5, 5, 5, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 0, 0, 0, 0, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 0, 0, 0, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 5, 5, 5, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 0, 0, 0, 0, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 0, 0, 0, 5, 0, 0, 0, 0, 0, 0, 0],
                // [0, 0, 0, 5, 5, 5, 5, 5, 0, 0, 0, 0, 0, 0, 7],
            ],
            alg: "depth-first",
            res: [],
            visited: [],
            mode: "",
            pathed: false,
            dragging: false
        };
    },
    methods: {
        async blockClicked([x, y], weight) {
            if (weight == 0) this.$set(this.grid[y], x, 5);
            if (weight == 5) this.$set(this.grid[y], x, 0);
            if (this.pathed) this.getPath(true);
        },
        async changeMode(newMode) {
            this.mode = newMode;
        },
        async getPath(nodelay = false) {
            try {
                let {
                    data: { visited }
                } = await axios.post("http://localhost:3000/getPath", {
                    alg: this.alg,
                    grid: this.grid
                });
                let {
                    data: { s }
                } = await axios.post("http://localhost:3000/getPath", {
                    alg: this.alg,
                    grid: this.grid
                });

                this.clearPath();
                this.pathed = true;
                this.res = this.grid.map(row => row.map(() => 0));
                await this.animate(visited, 100, nodelay);
            } catch (error) {
                this.clearPath();
                this.pathed = true;
            }
        },
        async clearPath() {
            this.res = lodash.cloneDeep(this.grid);
            this.pathed = false;
        },
        async animate(visited, ms, nodelay = false) {
            let timer = ms => {
                return new Promise(res => setTimeout(res, ms));
            };
            for (let [x, y] of visited) {
                if (this.res[y][x] != -2) {
                    this.$set(this.res[y], x, -2);
                    if (!nodelay) await timer(ms);
                }
            }
        }
    },
    watch: {
        res: {
            handler() {
                this.k += 1;
            },
            deep: true
        },
        grid: {
            handler() {
                this.k += 1;
            },
            deep: true
        }
    },
    computed: {},
    async created() {
        this.clearPath();
    },
    async beforeCreate() {
        let result = await axios.post("http://localhost:3000/getMaze", {
            x: 11,
            y: 19
        });
        console.log(result);
        this.grid = result.data;
        this.res = lodash.cloneDeep(this.grid);
        window.addEventListener("mouseup", () => (this.dragging = false));
    }
};
</script>

<style>
.grid-container {
    width: 40%;
}
</style>