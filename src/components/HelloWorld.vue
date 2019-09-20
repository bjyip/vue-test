<template>
    <div class="hello">
        <el-table :data="banjis" border style="width: 100%">
            <el-table-column prop="banji" label="班级" width="180"></el-table-column>
            <el-table-column label="班主任" width="180">
                <template slot-scope="prop">
                    <el-row v-for="(item, index) in prop.row.headmaster" :key="index">
                        <el-autocomplete
                            v-if="item.buttonInput"
                            v-model="item.name"
                            placeholder="请输入内容"
                            :trigger-on-focus="false"
                            :fetch-suggestions="fetchSuggestions"
                            :class="{'button-input': item.buttonInput}"
                            @dblclick.native="dblclick($event, item)"
                            @blur="blur($event, item, index, prop.row.headmaster)"
                            @keyup.native.ctrl.67="ctrlC($event, item)"
                            @keyup.native.Meta.67="ctrlC($event, item)"
                        ></el-autocomplete>
                    </el-row>
                    <el-row>
                        <el-autocomplete
                            v-model="prop.row.headmaster[prop.row.headmaster.length - 1].name"
                            placeholder="请输入内容"
                            :trigger-on-focus="false"
                            :fetch-suggestions="fetchSuggestions"
                            @paste.native="onpaste($event, prop.$index, 'headmaster')"
                        ></el-autocomplete>
                    </el-row>
                </template>
            </el-table-column>
            <el-table-column label="语文" width="180">
                <template slot-scope="prop">
                    <el-row v-for="(item, index) in prop.row.chinese" :key="index">
                        <el-autocomplete
                            v-if="item.buttonInput"
                            v-model="item.name"
                            placeholder="请输入内容"
                            :class="{'button-input': item.buttonInput}"
                            :trigger-on-focus="false"
                            :fetch-suggestions="fetchSuggestions"
                            @dblclick.native="dblclick($event, item)"
                            @blur="blur($event, item, index, prop.row.chinese)"
                            @keyup.native.ctrl.67="ctrlC($event, item)"
                            @keyup.native.Meta.67="ctrlC($event, item)"
                        ></el-autocomplete>
                    </el-row>
                    <el-row>
                        <el-autocomplete
                            v-model="prop.row.chinese[prop.row.chinese.length - 1].name"
                            placeholder="请输入内容"
                            :trigger-on-focus="false"
                            :fetch-suggestions="fetchSuggestions"
                            @paste.native="onpaste($event, prop.$index, 'chinese')"
                        ></el-autocomplete>
                    </el-row>
                </template>
            </el-table-column>
        </el-table>
        <input type="button"
            value="123"
            @keydown="keydown"
            @keyup="keyup"
        >
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
            isCtrl: false,
            isC: false
        })
    },
    mounted() {
        this.setBtnInput(); 
    },
    methods: {
        keydown(e) {
            if (e.keyCode === 17 || e.keyCode === 91) {
                this.isCtrl = true;
            }
            if (e.keyCode === 67) {
                this.isC = true;
            }
            if (this.isCtrl && this.isC) {
                console.log('ctrlC')
            }
        },
        keyup() {
            console.log('up')
            this.isCtrl = this.isC = false;
        },
        fetchSuggestions(queryString, callback) {
            if (!queryString) {
                return;
            }
            callback([
                {
                    value: queryString + 1,
                },
                {
                    value: queryString + 2,
                }
            ])
        },
        blur(e, item, index, arr) {
            if (Object.keys(item).length === 0 || item.name === '') {
                arr.splice(index, 1);
                return;
            }
            e.target.setAttribute('type', 'button');
        },
        dblclick(e) {
            e.target.setAttribute('type', 'text');
        },
        ctrlC(e, item) {
            if (e.target.getAttribute('type') === 'button') {
                this.copyValue = _.cloneDeep(item);
                const inputElement = document.createElement('input');
                inputElement.setAttribute('id', 'cp_hgz_input');
                inputElement.value = `${this.copyValue.name}/${this.copyValue.phone}`;
                document.getElementsByTagName('body')[0].appendChild(inputElement);
                document.getElementById('cp_hgz_input').select();
                document.execCommand('copy');
                document.getElementById('cp_hgz_input').remove();
            }
        },
        onpaste(e, index, type) {
            const pasteValue = e.clipboardData.getData('text/plain');
            const copyValue =  _.cloneDeep(this.copyValue);
            if (pasteValue === `${copyValue.name}/${copyValue.phone}`) {
                setTimeout(() => {
                    e.target.select();
                    document.execCommand('delete');
                    this.banjis[index][type].splice(this.banjis[index][type].length - 1, 0, copyValue)
                    this.setBtnInput();
                }, 300);
            }
        },
        setBtnInput() {
            this.$nextTick(() => {
                const autocompleteNode = document.querySelectorAll('.button-input.el-autocomplete');
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
