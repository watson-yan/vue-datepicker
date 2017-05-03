<template>
    <div class="vue-datepicker">
        <input @click.stop="show=!show" :value="current | dateFormat" type="text" readonly>
        <div v-if="show" class="vue-datepicker-wrap">
            <div class="vue-datepicker-header" @click.stop="">
                <span @click.stop="switchMonth(-1)" class="vue-datepicker-header-btn vue-datepicker-header-btn-pre">&lt;</span>
                <span @click.stop="selectYear=!selectYear"
                     class="vue-datepicker-header-btn vue-datepicker-header-btn-day">
                    {{select.year}} 年 {{select.month}} 月
                </span>
                <span @click.stop="switchMonth(1)" class="vue-datepicker-header-btn vue-datepicker-header-btn-next">&gt;</span>
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
                            <td v-for="weekday of week" @click="pick(weekday)"
                                :class="{
                                    'flag': weekday.flag, 
                                    'active': !weekday.flag && weekday.text == current.date
                                         && select.month == current.month && select.year == current.year}">
                                {{weekday.text}}
                            </td>
                        </tr>
                    </tbody>
                </table>
                <div v-if="selectYear" class="vue-date-picker-year-panel">
                    <ul ref="year">
                        <li v-for="y of years" @click.stop="pickYear(y)" :class="{'active': y == select.year}">{{y}}</li>
                    </ul>
                    <ul ref="month">
                        <li v-for="(m, $index) of months" 
                            @click.stop="pickMonth($index + 1)" 
                            :class="{'active': $index + 1 == select.month}">
                            {{m}}
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>
<style lang="sass">
    .vue-datepicker {
        &>input {
            padding: 5px 10px;
            width: 200px;
            line-height: 24px;
            border: 1px solid #BFCBD7;
            border-radius: 3px;
            font-size: 14px;
            outline: none;
            cursor: pointer;
            &:focus {
                border: 1px solid #20a0ff;
            }
        }
        .vue-datepicker-wrap {
            width: 240px;
            box-shadow: 2px 2px 8px #bdb8b8;
            z-index: 999;
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
                position: relative;
                table {
                    width: 100%;
                    border-collapse: collapse;
                    thead {
                        line-height: 30px;
                        font-size: 12px;
                        background: #eee;
                    }
                    tbody {
                        tr {
                            line-height: 28px;
                            td {
                                font-size: 12px;
                                text-align: center;
                                cursor: pointer;
                                &.active, &.active:hover {
                                    color: #fff;
                                    background: #008afe;
                                }
                                &.flag {
                                    color: #999;
                                }
                            }
                            td:hover {
                                background: #eee;
                            }
                        }
                    }
                }
                .vue-date-picker-year-panel {
                    position: absolute;
                    top: 0;
                    left: 0;
                    width: 100%;
                    height: 100%;
                    background: #fff;
                    ul {
                        width: 50%;
                        height: 100%;
                        margin: 0;
                        padding-left: 0;
                        box-sizing: border-box;
                        overflow-y: auto;
                        float: left;
                        list-style: none;
                        li {
                            font-size: 14px;
                            text-align: center;
                            line-height: 30px;
                            cursor: pointer;
                            &.active {
                                color: #fff;
                                background: #007acc;
                            }
                        }
                        &:first-child {
                            border-right: 1px solid #007acc;
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
                type: Number,
                default() {
                    return new Date().getTime()
                }
            }
        },
        data() {
            return {
                show: false,    // 控制日历面板的显示与隐藏
                selectYear: false,  // 控制年份的面板的显示和隐藏
                current: '',    // 已选择的日期时间。yyyy-MM-dd
                select: {       // 已选择的日期对象
                    year: '',
                    month: '',
                    date: '',
                    day: ''
                },
                currentMonthFirstDay: null, // 当前月的1号属于星期几
                currentMonthEndDate: null,  // 当前月的最后一天是几号
                currentMonthEndDay: null,   // 当前月的最后一天属于星期几
                lastMonthEndDate: null,     // 上个月的最后一天是几号
                list: [],   // 日历的二维数组
                years: [],  // 1900-2100
                months: ['一月','二月','三月','四月','五月','六月','七月','八月','九月','十月','十一月','十二月']
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
            },
            show(newVal) {
                if (newVal) {
                    document.addEventListener('click', this.bindEvent)
                } else {
                    document.removeEventListener('click', this.bindEvent)
                }
            },
            // 打开年份选择器的时候使当前年份、月份出现在窗口顶部
            selectYear(newVal) {
                if (newVal) {
                    this.$nextTick(() => {
                        const year = this.$refs.year
                        const month = this.$refs.month
                        const y = year.getElementsByClassName('active')[0].innerHTML
                        const m = month.getElementsByClassName('active')[0].innerHTML
                        year.scrollTop = (y - 1900) * 30
                        month.scrollTop = (this.select.month - 1) * 30
                    })
                }
            }
        },
        created() {
            this.transform(this.moment)
            this.complete()
            // 获得年份列表： 1900-2100
            for(let i = 1900; i <= 2100; i++) {
                this.years.push(i)
            }
        },
        filters: {
            // 日期格式过滤器
            dateFormat(val) {
                 if (!val) {
                     return ''
                 }
                 return `${val.year}-${val.month}-${val.date}`.replace(/\d+/g, (a) => {
                     return (a.length === 4) ? a : ((a.length === 2) ? a : ('0' + a))
                 })
            }
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
                    if (i === lineNumber - 1 && nextDays > 0 && nextDays !== 7) {
                        this.list[lineNumber] = 
                            this.numberList(temp, this.currentMonthEndDate)
                            .concat(this.numberList(1, nextDays, true))
                    } else {
                        this.list.push(this.numberList(temp, temp + 6))
                    }
                    temp = temp + 7
                }
            },
            /*
            * 获得日历数组
            * start: 开始日
            * end: 结束日
            * flag： boolean值，判断是否属于本月
            */
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
            /*
            * 切换月份， -1为当前月份-1，+1为当前月份+1
            */
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
            },
            pick(day) {
                if (!!day.flag) {
                    // 当页日历上可能还会显示部分上个月或者下个月的部分天数，根据标识来做判断
                    // 并对月份作出相应的处理
                    if (parseInt(day.text) > 15) {
                        this.transform(new Date(this.select.year, this.select.month - 2, parseInt(day.text)))
                    } else {
                        this.transform(new Date(this.select.year, this.select.month, parseInt(day.text)))
                    }
                } else {
                    this.transform(new Date(this.select.year, this.select.month - 1, parseInt(day.text)))
                }
                this.complete()
            },
            // 绑定事件：点击关闭日历面板
            bindEvent() {
                this.show = false
                this.selectYear = false
            },
            // 选取年
            pickYear(n) {
                this.transform(new Date(n, this.select.month - 1, this.select.date))
                this.complete()
            },
            // 选取月
            pickMonth(n) {
                this.transform(new Date(this.select.year, n - 1, this.select.date))
                this.complete()
            },
            // 更改选中时间并向父组件派发事件
            complete() {
                // 触发父组件的传过来的picked事件。三个参数: 年，月，日
                this.$emit('picked', this.select.year, this.select.month, this.select.date)
                this.current = {
                    year: this.select.year,
                    month: this.select.month,
                    date: this.select.date
                }
            }
        }
    }
</script>