<template>
    <div class="vue-datepicker">
        <input type="text">
        <div class="vue-datepicker-wrap">
            <div class="vue-datepicker-header">
                <span @click="switchMonth(-1)" class="vue-datepicker-header-btn vue-datepicker-header-btn-pre">&lt;</span>
                <span class="vue-datepicker-header-btn vue-datepicker-header-btn-day">
                    {{select.year}} 年 {{select.month}} 月
                </span>
                <span @click="switchMonth(1)" class="vue-datepicker-header-btn vue-datepicker-header-btn-next">&gt;</span>
            </div>
            <div class="vue-datepicker-content">
                <table>
                    <thead>
                        <th>日</th>
                        <th>一</th>
                        <th>二</th>
                        <th>三</th>
                        <th>四</th>
                        <th>五</th>
                        <th>六</th>
                    </thead>
                    <tbody>
                        <tr v-for="week of list">
                            <td v-for="weekday of week" 
                                :class="{'flag': weekday.flag, 'active': !weekday.flag && weekday.text == select.date}">
                                {{weekday.text}}
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>
<style lang="sass">
    .vue-datepicker {
        .vue-datepicker-wrap {
            width: 240px;
            box-shadow: 2px 2px 8px #bdb8b8;
            .vue-datepicker-header {
                padding: 0px 15px;
                font-size: 14px;
                text-align: center;
                line-height: 36px;
                border-bottom: 1px solid #ccc;

                .vue-datepicker-header-btn {
                    cursor: pointer;
                    &:hover {
                        color: #008afe;
                    }
                }
                .vue-datepicker-header-btn-pre {
                    float: left;
                }
                .vue-datepicker-header-btn-day {

                }
                .vue-datepicker-header-btn-next {
                    float: right;
                }
            }
            .vue-datepicker-content {
                table {
                    width: 100%;
                    border-collapse: collapse;
                    thead {
                        line-height: 30px;
                        font-size: 12px;
                        background: #e1edfa;
                    }
                    tbody {
                        tr {
                            line-height: 28px;
                            td {
                                font-size: 12px;
                                text-align: center;
                                cursor: pointer;
                                border: 1px solid #ccc;
                                &.active, &.active:hover {
                                    color: #fff;
                                    background: #008afe;
                                }
                                &.flag {
                                    color: #888;
                                    background: #f0f0f0;
                                }
                            }
                            td:hover {
                                background: #eee;
                            }
                        }
                    }
                }
            }
        }
    }
</style>
<script>
    export default {
        props: {
            moment: {
                type: String,
                default() {
                    return new Date().toLocaleString()
                }
            }
        },
        data() {
            return {
                select: {
                    year: '',
                    month: '',
                    date: '',
                    day: ''
                },
                currentMonthFirstDay: null,
                currentMonthEndDate: null,
                currentMonthEndDay: null,
                lastMonthEndDate: null,
                list: []
            }
        },
        watch: {
            select: {
                handler(newVal) {
                    let pre
                    if (newVal.month == 1) {
                        pre = new Date(newVal.year - 1, 12, 0)
                    } else {
                        pre = new Date(newVal.year, newVal.month - 1, 0)
                    }
                    this.lastMonthEndDate = pre.getDate()
                    // 获取日历排表
                    this.getDateList()
                },
                deep: true
            }
        },
        created() {
            this.transform(this.moment)
        },
        methods: {
            /**
            * 将时间转化为具体的 年、月、日、星期
            **/
            transform(time) {
                const date = new Date(time)
                this.select.year = date.getFullYear()
                this.select.month = date.getMonth() + 1
                this.select.date = date.getDate()
                this.select.day = date.getDay()
                this.currentMonthFirstDay = 
                    new Date(this.select.year, this.select.month - 1, 1, 0).getDay()
                this.currentMonthEndDate = 
                    new Date(this.select.year, this.select.month, 0).getDate()
                this.currentMonthEndDay =
                    new Date(this.select.year, this.select.month, 0).getDay()
            },
            /*
            * 计算出日历列表，二维数组
            * 第一层为星期，第二层为每星期的第几天
            */
            getDateList() {
                this.list = []
                // 获取日历第一行的数据（需加上第一个星期中所包含上个月的几天）
                let temp = this.lastMonthEndDate - (this.currentMonthFirstDay - 1)
                let list = 
                    this.numberList(temp, this.lastMonthEndDate, true)
                    .concat(this.numberList(1, 7 - this.currentMonthFirstDay))

                this.list.push(list)
                temp = (7 - this.currentMonthFirstDay) + 1
                
                /*
                * 剩下的行数
                */
                // 计算除了第一行剩下的天数
                const leftDays = this.currentMonthEndDate - (7 - this.currentMonthFirstDay)
                // 剩下的星期数
                const lineNumber = Math.ceil(leftDays / 7)
                // 包含下个月日历的天数
                const nextDays = 7 - (leftDays % 7)

                for (let i = 0; i < lineNumber; i++) {
                    if (i === lineNumber - 1 && nextDays > 0) {
                        this.list[lineNumber] = 
                            this.numberList(temp, this.currentMonthEndDate)
                            .concat(this.numberList(1, nextDays, true))
                    } else {
                        this.list.push(this.numberList(temp, temp + 6))
                    }
                    temp = temp + 7
                }
            },
            numberList(start, end, flag) {
                let list = []
                for (let i = start; i <= end; i++) {
                    list.push({
                        text: i,
                        flag: !!flag
                    })
                }
                return list
            },
            switchMonth(n) {
                let year = this.select.year
                if (n===-1) {
                    const pre = this.select.month === 1 ? 12 : this.select.month - 1
                    if (pre === 12) {
                        this.transform(new Date(year - 1, pre - 1, this.select.date))
                    } else {
                        this.transform(new Date(year, pre - 1, this.select.date))
                    }

                } else {
                    const next = this.select.month === 12 ? 1 : this.select.month + 1
                    if (next === 1) {
                        this.transform(new Date(year + 1, next - 1, this.select.date))
                    } else {
                        this.transform(new Date(year, next - 1, this.select.date))
                    }
                }
            }
        }
    }
</script>