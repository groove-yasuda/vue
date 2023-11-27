<template>
    <v-app>
        <v-main>
            <v-container>

                <div>
                    <v-icon>mdi-account-plus</v-icon>
                    <p>工場システム</p>
                    <p>工場登録画面</p>
                </div>



                <br>

                <input-field label="正式名称" v-model="input_Emp_Name" :hint="Name_Hint" />

                <br>
                <v-row>

                    &nbsp;&nbsp;
                    <div>
                        <select_Dropdown v-model="selected_Year" label="創立年月日:" id="mySelect" selectClass="select-Dropdown" :options="year_Range" />
                    </div>

                    <div>
                        <select_Dropdown v-model="selected_Month" label="/" id="mySelect" selectClass="select-Dropdown" :options="months" />
                    </div>

                    <div>
                        <select_Dropdown v-model="selected_Day" label="/" id="mySelect" selectClass="select-Dropdown" :options="day_Range" />
                    </div>

                </v-row>

                <br>

                <div v-for="(textbox, index) in textboxes" :key="index" class="textbox-container">
                    <input-field label="通称" v-model="textboxes[index].value" :hint="Common_Hint" />
                </div>
                <br><br>




                <v-row>
                    <v-col cols="auto">
                        <v-btn v-on:click="add_Textbox">行 追加</v-btn>&nbsp;&nbsp;&nbsp;
                        <v-btn v-on:click="remove_Textbox">行 削除</v-btn>
                        <br><br>
                        <v-btn v-on:click="INSERT">登録</v-btn>
                    </v-col>
                </v-row>


                <p v-if="error_Message" class="error-message">{{ error_Message }}</p>

            </v-container>


                                  
        </v-main>
    </v-app>
</template>


<style type="text/css">
    p {
        color:black;
    }
    .error-Message {
        color: red;
    }
    .select-Dropdown {
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: white;
        font-size: 18px;
        width: 80px;
    }
    .textbox-container {
        white-space: pre-line;
    }
</style>

<script>
    import InputField from '@/components/input_Field.vue';
    import select_Dropdown from '@/components/select_Dropdown.vue';
    import axios from 'axios';

    export default {
        components: {
            InputField,
            select_Dropdown,
        },

        data() {
            return {
                input_Emp_Name: '',
                textboxes: [{ value: '' }],
                Common_Hint: '会社名の通称40文字以内の全角文字で入力して下さい',
                Name_Hint: '会社名を40文字以内の全角文字で入力して下さい',
                years: [1990], // 生年月日の年の選択肢
                months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12], // 生年月日の月の選択肢
                days: [1], // 生年月日の日の選択肢
                selected_Year: 2000, // 選択された年
                selected_Month: 1, // 選択された月
                selected_Day: 1, // 選択された日
                error_Message:'',
            };
        },

        created() {
            // ルーターからパラメータを取得してデータに代入
            this.error_Message = this.$route.params.error_Message;
        },

        computed: {
            year_Range() {
                const current_Year = new Date().getFullYear();
                const start_Year = current_Year - 150; // 100年分の範囲を生成
                return Array.from({ length: 151 }, (_, i) => start_Year + i);
            },
            day_Range() {
                return Array.from({ length: 31 }, (_, i) => i + 1); // 日の選択肢を1から31まで生成
            },
        },

        methods: {
            add_Textbox() {
                if (this.textboxes.length < 3) {
                    this.textboxes.push({ value: '' });
                }
            },
            remove_Textbox() {
                this.textboxes.splice(1,1);
            },
            INSERT() {
                const birth = this.selected_Year + '/' + this.selected_Month + '/' + this.selected_Day;
                let hasError = false; 


                if (!/^[一-龯ぁ-んァ-ヶー]*$/.test(this.input_Emp_Name) || 1 > this.input_Emp_Name.length || this.input_Emp_Name.length > 40 ) {
                    this.name_Error = true;
                    hasError = true;
                } else {
                    this.name_Error = false;
                }

                if (!/^[一-龯ぁ-んァ-ヶー]*$/.test(this.textboxes[0].value) || this.textboxes[0].value > 40) {
                    this.name_Error = true;
                    hasError = true;
                } else {
                    this.name_Error = false;
                }


                if (this.textboxes.length === 1) {
                    if (this.textboxes[0].value === "") {
                        this.textboxes[0].value = this.input_Emp_Name;
                    }
                }

                if (this.textboxes.length === 2) {
                    if (this.textboxes[0].value === this.textboxes[1].value) {
                        hasError = true;
                    } else {
                        hasError = false;
                    }
                }

                if (this.textboxes.length === 3) {
                    if (this.textboxes[0].value === this.textboxes[1].value || this.textboxes[1].value === this.textboxes[2].value || this.textboxes[0].value === this.textboxes[2].value) {
                        hasError = true;
                    } else {
                        hasError = false;
                    }
                }
               
                if (!hasError) {
                    let common_Name0;
                    let common_Name1;
                    let common_Name2;

                    common_Name0 = this.textboxes[0].value;
                    common_Name1 = this.textboxes[1].value;
                    common_Name2 = this.textboxes[2].value;
                    axios
                        .request({
                            method: 'POST',
                            url: 'http://localhost:8080/insert_Factory',
                            data: {
                                factory_Name: this.input_Emp_Name,
                                common_Name0: common_Name0,
                                common_Name1: common_Name1,
                                common_Name2: common_Name2,
                                birth: birth,
                            }
                        })
                        .then((response) => {
                            if (response.data === true) {
                                const error_Message = '登録が完了しました。';
                                this.error_Message = error_Message;
                                this.dataReset()
                            }
                            else if (response.data === false) {
                                const error_Message = '入力された工場名はすでに登録されています。';
                                this.error_Message = error_Message;
                                this.dataReset()
                            }

                        })
                }
            },
            dataReset() {
                const name = '';
                this.textboxes = [{ value: '' }];
                this.$nextTick(() => {
                    this.input_Emp_Name = name;
                    console.log(this.input_Emp_Name);
                });
            }
        },
        }

</script>