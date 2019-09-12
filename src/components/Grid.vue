<template>
    <div id="grid">
        {{k}}
        <span>Grid: {{visited}}</span>
        <br />
        <span>Res: {{res}}</span>
        <div class="grid-container">
            <div v-bind:key="i" v-for="(row, i) in res" class="grid-row">
                <Block :weight="grid[i][j]" :type="col" v-bind:key="j" v-for="(col, j) in row"></Block>
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
            visited: [],
            grid: [
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
                [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0],
                [0, 0, 0, 1, 0, 1, 0, 1, 1, 1, 1],
                [0, 0, 0, 1, 0, 1, 0, 1, 0, 1, 0],
                [3, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0],
                [0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0],
                [0, 0, 0, 1, 1, 1, 1, 1, 0, 0, 0],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
            ],
            res: []
        };
    },
    methods: {
        async animate(res, visited, ms) {
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
                        this.$set(this.res[y], x, 1)
                    );
                    await timer(ms);
                }
                if (this.res[y][x] != 2) {
                    this.$set(this.res[y], x, 2);
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
    async mounted() {
        let {
            data: { visited }
        } = await axios.post("http://localhost:3000/getPath", {
            alg: "depth-first",
            start: [0, 0],
            end: [10, 8],
            grid: this.grid
        });
        this.res = this.grid.map(row => row.map(col => 0));
        this.animate(this.res, visited, 0);
    }
};
</script>

<style>
.grid-row {
    width: 500px;
    display: grid;
    grid-template-columns: auto auto auto auto auto auto auto auto auto auto auto;
    /* grid-gap: 10px; */
    background-color: white;
    /* padding: 5px 10px; */
}
</style>