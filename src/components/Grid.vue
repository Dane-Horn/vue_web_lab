<template>
    <div id="grid">
        <div class="grid-container">
            <div :key="i" v-for="(row, i) in res" class="grid-row">
                <Block
                    v-on:get-path="getPath()"
                    v-on:clear-path="clearPath()"
                    :seen="col == -1"
                    :visited="col == -2"
                    :weight="grid[i][j]"
                    :key="j"
                    v-for="(col, j) in row"
                ></Block>
            </div>
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
            grid: [
                [0, 0, 3, 0, 0, 0, 0, 0, 0, 0, 6],
                [5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 0],
                [1, 0, 0, 0, 0, 5, 0, 0, 0, 0, 0],
                [2, 0, 5, 5, 0, 5, 0, 5, 0, 0, 5],
                [3, 0, 0, 5, 0, 5, 0, 5, 5, 5, 5],
                [5, 0, 0, 5, 0, 5, 0, 5, 0, 1, 2],
                [5, 0, 0, 5, 0, 0, 0, 5, 0, 2, 3],
                [0, 0, 0, 5, 5, 5, 5, 5, 0, 2, 4],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 2, 7]
            ],
            alg: "depth-first",
            res: []
        };
    },
    methods: {
        async getPath() {
            this.res = lodash.cloneDeep(this.grid);
            let {
                data: { visited }
            } = await axios.post("http://localhost:3000/getPath", {
                alg: this.alg,
                grid: this.grid
            });
            this.res = this.grid.map(row => row.map(() => 0));
            this.animate(visited, 0);
        },
        async clearPath() {
            this.res = lodash.cloneDeep(this.grid);
        },
        async animate(visited, ms) {
            let timer = ms => {
                return new Promise(res => setTimeout(res, ms));
            };
            for (let {
                val: [x, y],
                neighbours
            } of visited) {
                neighbours = (neighbours || []).filter(
                    ([x, y]) => this.res[y][x] == 0
                );
                if (neighbours.length > 0) {
                    neighbours.forEach(([x, y]) =>
                        this.$set(this.res[y], x, -1)
                    );
                    await timer(ms);
                }
                if (this.res[y][x] != 2) {
                    this.$set(this.res[y], x, -2);
                    await timer(ms);
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
        }
    },
    computed: {},
    async created() {
        this.clearPath();
    }
};
</script>

<style>
.grid-row {
    width: 500px;
    display: grid;
    grid-template-columns: repeat(11, auto);
    background-color: white;
}
</style>