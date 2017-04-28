<template>
    <div class="vue-datepicker">
        <input type="text">
        <div class="vue-datepicker-wrap">
            <div class="vue-datepicker-header">
                <span class="vue-datepicker-header-btn vue-datepicker-header-btn-pre">&lt;</span>
                <span class="vue-datepicker-header-btn vue-datepicker-header-btn-day">2017-4</span>
                <span class="vue-datepicker-header-btn vue-datepicker-header-btn-next">&gt;</span>
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
                        <tr>
                            <td>30</td>
                            <td>31</td>
                            <td>1</td>
                            <td>2</td>
                            <td>3</td>
                            <td>4</td>
                            <td>5</td>
                        </tr>
                        <tr>
                            <td>6</td>
                            <td>7</td>
                            <td>8</td>
                            <td>9</td>
                            <td>10</td>
                            <td>11</td>
                            <td>12</td>
                        </tr>
                        <tr>
                            <td>13</td>
                            <td>14</td>
                            <td>15</td>
                            <td>16</td>
                            <td>17</td>
                            <td>18</td>
                            <td>19</td>
                        </tr>
                        <tr>
                            <td>20</td>
                            <td>21</td>
                            <td>22</td>
                            <td>23</td>
                            <td>24</td>
                            <td>25</td>
                            <td>26</td>
                        </tr>
                        <tr>
                            <td>27</td>
                            <td>28</td>
                            <td>29</td>
                            <td>30</td>
                            <td>31</td>
                            <td>1</td>
                            <td>2</td>
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
                                &.active {
                                    color: #fff;
                                    background: #008afe;
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
        data() {
            return {
                
            }
        }
    }
</script>