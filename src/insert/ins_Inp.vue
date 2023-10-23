<template>
    <v-app>
        <v-main>
            <v-container>

                <v-icon>mdi-account-plus</v-icon>
                <p>社員情報登録</p>
                <p>社員情報入力画面</p>


                <v-text-field label="社員ID" clearable v-model="input_Emp_Id" @input="check_Emp_Id" class="custom-Hint-Style"
                              hint=" 社員IDをA001～Z999の間の半角英数字で入力して下さい。
                              *社員IDは大文字のアルファベット＋三桁の数字の組み合わせです"></v-text-field>
                <p v-if="id_Error" class="error-Message">有効な社員IDを入力してください。</p>

                <input-field label="社員ID" v-model="employeeId" :hint="employeeIdHint" :error="employeeIdError" />


                <br>
                <v-row justify="center">
                    <div>

                        <birthDropdown></birthDropdown>

                    </div>
                    &nbsp;&nbsp;&nbsp;
                    <div>
                        <label for="age">年齢:</label>
                        <select v-model="selected_Age" id="age" class="select-Dropdown">
                            <option v-for="age in age_Range" :value="age" :key="age">{{ age }} 歳</option>
                        </select>
                    </div>
                    &nbsp;&nbsp;&nbsp;
                    <div>
                        <label for="age">性別:</label>
                        <select v-model="selected_Gender" id="gender" class="select-Dropdown">
                            <option v-for="gender in gender_Option" :value="gender" :key="gender">{{ gender }}</option>
                        </select>
                    </div>
                </v-row>
                <br><br>

                <v-text-field label="基本給" clearable v-model="input_Basic_Salary" @input="check_Basic_Salary"
                              hint=" 半角数字で入力して下さい"
                              class="custom-Hint-Style"></v-text-field>
                <p v-if="basic_Salary_Error" class="error-Message">有効な値を入力してください。</p>

                <v-text-field label="交通費" clearable v-model="input_Transport_Expens" @input="check_Transport_Expens"
                              hint="半角数字で入力して下さい"
                              class="custom-Hint-Style"></v-text-field>
                <p v-if="transport_Expens_Error" class="error-Message">有効な値を入力してください。</p>
                <br><br>


                <v-row justify="center">
                    <v-col cols="auto">
                        <v-btn v-on:click="INSERT">登録</v-btn>
                    </v-col>
                </v-row>


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
        padding: 5px; /* パディングを追加して選択肢が見やすくなります */
        border: 1px solid #ccc; /* ボーダーを追加してプルダウンの境界を明示的に表示します */
        border-radius: 4px; /* 角を丸くすることで視覚的に魅力的になります */
        background-color: white; /* 背景色を白に設定します */
        font-size: 18px; /* フォントサイズを調整できます */
        width: 65px; /* 幅を調整できます */
    }
    .custom-Hint-Style {
        font-size: 15px; /* ヒントの文字サイズを設定 */
        color: #333;
    }

</style>

<script>
    import InputField from '@/components/input_Field.vue';
    import birthDropdown from '@/components/birth_Dropdown.vue';

    export default {
        components: {
            InputField,
            birthDropdown,
        },
        data() {
            return {
                employeeIdHint: '社員IDをA001～Z999の間の半角英数字で入力して下さい。* 社員IDは大文字のアルファベット＋三桁の数字の組み合わせです',
                employeeId: '',
                input_Emp_Id: '',
                id_Error: false,
                input_Emp_Name: '',
                name_Error: false,
                input_Basic_Salary: '',
                basic_Salary_Error: false,
                input_Transport_Expens: '',
                transport_Expens_Error: false,
                years: [1990], // 生年月日の年の選択肢
                months: [1,2,3,4,5,6,7,8,9,10,11,12], // 生年月日の月の選択肢
                days: [1], // 生年月日の日の選択肢
                selected_Year: 2000, // 選択された年
                selected_Month: 1, // 選択された月
                selected_Day: 1, // 選択された日
                selected_Age: 18,//選択された年齢
                selected_Gender: '男性',
                gender_Option: ['男性', '女性'],
            };
        },
        computed: {
            year_Range() {
                const current_Year = new Date().getFullYear();
                const start_Year = current_Year - 100; // 100年分の範囲を生成
                return Array.from({ length: 101 }, (_, i) => start_Year + i);
            },
            age_Range() {
                // 年齢の範囲を設定します
                const min_Age = 1;
                const max_Age = 100;

                // 年齢の範囲から選択肢の配列を生成します
                const range = [];
                for (let age = min_Age; age <= max_Age; age++) {
                    range.push(age);
                }

                return range;
            },
            day_Range() {
                return Array.from({ length: 31 }, (_, i) => i + 1); // 日の選択肢を1から31まで生成
            },
        },
        methods: {
            check_Emp_Name() {
                if (!/^[一-龯ぁ-んァ-ヶー]*$/.test(this.input_Emp_Name)) {
                    this.name_Error = true;
                } else {
                    this.name_Error = false;
                }
            },
            check_Basic_Salary() {
                if (!/^[0-9]*$/.test(this.input_Basic_Salary)) {
                    this.basic_Salary_Error = true;
                } else {
                    this.basic_Salary_Error = false;
                }
            },
            check_Transport_Expens() {
                if (!/^[0-9]*$/.test(this.input_Transport_Expens)) {
                    this.transport_Expens_Error = true;
                } else {
                    this.transport_Expens_Error = false;
                }
            },
            INSERT() {
                // 登録ボタンが押下されたときのみ社員IDのチェックを行う
                if (this.input_Emp_Id) {
                    if (!/^[A-Z]{1}[0-9]{3}$/.test(this.input_Emp_Id) || !/^(?!.*000).+$/.test(this.input_Emp_Id)) {
                        this.id_Error = true;
                        return;
                    } else {
                        this.id_Error = false;

                        const birth = this.selected_Year + '/' + this.selected_Month + '/' + this.selected_Day;
                        this.$router.push({
                            name: 'ins_Confi', params: {
                                emp_Id: this.input_Emp_Id, emp_Name: this.input_Emp_Name,
                                birth: birth, age: this.selected_Age, gender: this.selected_Gender,
                                basic_Salary: this.input_Basic_Salary, transport_Expens: this.input_Transport_Expens,
                            }
                        });
                    }
                } else {
                    this.id_Error = false;
                    return;
                }
            },
        },
        }

</script>