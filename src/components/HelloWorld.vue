<template>
    <div class="hello">
        <el-table :data="banjis" border style="width: 100%">
            <el-table-column prop="banji" label="班级" width="180"></el-table-column>
            <el-table-column label="班主任" width="180">
                <template slot-scope="prop">
                    <el-row v-for="(item, index) in prop.row.headmaster" :key="index" class="button-input">
                        <el-autocomplete
                            v-model="item.name"
                            placeholder="请输入内容"
                            :trigger-on-focus="item.editable"
                            :fetch-suggestions="fetchSuggestions"
                            :class="{'is-editable': item.editable}"
                            @dblclick.native="dblclick($event, item)"
                            @blur="blur($event, item)"
                            @keyup.native.ctrl.67="ctrlC($event, item)"
                        ></el-autocomplete>
                    </el-row>
                    <el-row>
                        <el-autocomplete
                            v-model="currentValue"
                            placeholder="请输入内容"
                            :fetch-suggestions="fetchSuggestions"
                            @keyup.native.ctrl.86="ctrlV($event, prop.$index, 'headmaster')"
                        ></el-autocomplete>
                    </el-row>
                </template>
            </el-table-column>
            <el-table-column label="语文" width="180">
                <template slot-scope="prop">
                    <el-row v-for="(item, index) in prop.row.chinese" :key="index" class="button-input">
                        <el-autocomplete
                            v-model="item.name"
                            placeholder="请输入内容"
                            :class="{'is-editable': item.editable}"
                            :trigger-on-focus="item.editable"
                            :fetch-suggestions="fetchSuggestions"
                            @dblclick.native="dblclick($event, item)"
                            @blur="blur($event, item)"
                            @keyup.native.ctrl.67="ctrlC($event, item)"
                        ></el-autocomplete>
                    </el-row>
                    <el-row>
                        <el-autocomplete
                            v-model="currentValue"
                            placeholder="请输入内容"
                            :fetch-suggestions="fetchSuggestions"
                            @keyup.native.ctrl.86="ctrlV($event, prop.$index, 'chinese')"
                        ></el-autocomplete>
                    </el-row>
                </template>
            </el-table-column>
        </el-table>
    </div>
</template>

<script>
import _data from '../data';
import { setTimeout } from 'timers';
const _ = require('lodash');
export default {
    name: "HelloWorld",
    data() {
        return Object.assign(_data, {
            currentValue: '',
            copyValue: {},
        })
    },
    mounted() {
        this.setBtnInput();
    },
    methods: {
        fetchSuggestions(queryString, callback) {
            if (!queryString) {
                return;
            }
            setTimeout(() => {
                callback([
                    {
                        value: queryString + 1,
                    },
                    {
                        value: queryString + 2,
                    }
                ])
            }, 500);
        },
        ctrlC(e, item) {
            console.log('ctrlC')
            if (e.target.getAttribute('type') === 'button') {
                console.log(item)
                this.copyValue = _.cloneDeep(item);
            }
        },
        ctrlV(e, index, type) {
            console.log('ctrlV')
            console.log(e, this.copyValue, index, type)
            this.banjis[index][type].push(this.copyValue);
            this.setBtnInput();
        },
        blur(e) {
            console.log('blur')
            e.target.setAttribute('type', 'button');
            this.disabled = true;
        },
        dblclick(e) {
            console.log('dblclick')
            this.disabled = false;
            e.target.setAttribute('type', 'text');
        },
        setBtnInput() {
            this.$nextTick(() => {
                const autocompleteNode = document.querySelectorAll('.button-input .el-autocomplete');
                autocompleteNode.forEach(input => {
                    const inputNode = input.querySelector('input');
                    inputNode.setAttribute('type', 'button');
                });
            });
        }
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    input {
        width: 80px;
    }
</style>
