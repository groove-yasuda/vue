<template>
    <div id="app">
        <!--  クリック要素  -->
        <span v-on:click="open" class="modal_open_btn">ここをクリック!!</span>
        <!--  モーダルウィンドウ  -->
        <div v-show="show" class="modal_contents">
            <!-- モーダルウィンドウの背景 -->
            <div v-on:click="close" class="modal_contents_bg"></div>
            <!--   モーダルウィンドウの中身   -->
            <div class="modal_contents_wrap">
                <v-row justify="center">
                    <p>モーダルウィンドウ</p>
                </v-row>

                <v-row>
                    <v-col cols="12">
                        <hot-table :data="salary_Karam" :settings="set" colWidths="200" rowHeights="45"></hot-table>
                        <hot-table :data="salary_Data" :settings="settings" colWidths="200" rowHeights="45"></hot-table>
                        <br>
                        <hot-table :data="deduction_Karam" :settings="set" colWidths="200" rowHeights="45"></hot-table>
                        <hot-table :data="deduction_Data" :settings="settings" colWidths="200" rowHeights="45"></hot-table>
                        <br>
                        <hot-table :data="total_Karam" :settings="set" colWidths="200" rowHeights="45"></hot-table>
                        <hot-table :data="total_Data" :settings="settings" colWidths="200" rowHeights="45"></hot-table>
                    </v-col>
                </v-row>

                <v-row justify="center">
                    <!--   モーダルウィンドウを閉じる   -->
                    <span v-on:click="close" class="modal_close_btn">閉じる</span>
                </v-row>
            </div>
        </div>
    </div>
</template>



<style>
    /* モーダルウィンドウを開く要素 */
    .modal_open_btn {
        display: inline-block;
        cursor: pointer;
        padding-top: 30px;
    }
    /* モーダルウィンドウ要素 */
    .modal_contents {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 100;
        width: 100%;
        height: 100%;
        width: 100%;
    }
    /* モーダルウィンドウの背景要素 */
    .modal_contents_bg {
        background: rgba(0,0,0,.8);
        width: 100%;
        height: 100%;
    }
    /* モーダルウィンドウの中身*/
    .modal_contents_wrap {
        position: absolute;
        top: 50%;
        left: 50%;
        background-color: #fff;
        width: 50%;
        height: 50%;
        margin: auto;
        transform: translate(-50%,-50%);
        padding: 30px;
    }
    /* モーダルウィンドウを閉じる要素 */
    .modal_close_btn {
        display: inline-block;
        cursor: pointer;
        margin-top: 10px;
    }
</style>

<script>
    export default {
        data() {
            return {
                //デフォルトはモーダルウィンドウを閉じる
                show: false
            }
        },
        methods: {
            //モーダルウィンドウを開く要素をクリックしたら
            open: function () {
                this.show = true
            },
            //モーダルウィンドウを閉じる要素をクリックしたら
            close: function () {
                this.show = false
            },
        }
    };
</script>

