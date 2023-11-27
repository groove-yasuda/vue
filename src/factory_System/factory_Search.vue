<template>
    <v-app>
        <v-main app class="custom-drawer">
            <v-container>
                <v-icon>mdi-account-search</v-icon>
                <p>工場情報検索画面</p>




                <select_Dropdown v-model="selected_Conditions" label="検索方法:" id="mySelect" selectClass="select-Dropdown-v1" :options="conditions" :style="{'color': 'black'}" />
                <br>

                <select_Dropdown v-model="selected_Target" label="検索項目:" id="mySelect" selectClass="select-Dropdown-v1" :options="target" :style="{'color': 'black'}" />
                <br>

                <input-field label="検索値" v-model="input_Emp_Search" :error="Error" />

                <br>
                <select_Dropdown v-model="selected_Sort" label="ソート:" id="mySelect" selectClass="select-Dropdown-v2" :options="sort" :style="{'color': 'black'}" />

                <br><br>
                <v-btn v-on:click="SEARCH">検索</v-btn>


            </v-container>


            <p v-if="Message" class="message">{{ Message }}</p>


        

                <v-container>

                    <v-data-table :headers="headers" :items="desserts" :items-per-page="items_Per_Page"
                                  class="elevation-10" height="600px"
                                  :footer-props="{ showFirstLastPage: true, firstIcon: 'mdi-arrow-collapse-left',
                                          lastIcon: 'mdi-arrow-collapse-right', prevIcon: 'mdi-minus', nextIcon: 'mdi-plus'}">

                        <template v-slot:item="{ item }">
                            <tr>
                                <td class="text-start" :style="{'color': 'black'}">{{ item['工場ID'] }}</td>
                                <td class="text-start" :style="{'color': 'black'}">{{ item['正式名称'] }}</td>
                                <td class="text-start" :style="{'color': 'black'}">{{ item['創立年月日'] }}</td>
                                <td class="text-start" :style="{'color': 'black'}" v-html="item['通称']"></td>
                                <td class="text-start" :style="{'color': 'black'}">{{ item['創立周年'] }}</td>
                                <td>
                                    <select v-model="item.selected_Factory" class="select-Dropdown-v3" @change="handleFactoryChange(item)">
                                        <option v-for="option in options" :value="option" :key="option">{{ option }}</option>
                                    </select>
                                </td>
                            </tr>
                        </template>
                    </v-data-table>
                </v-container>

            <br>



            <v-container>
                <router-link :to="{ name: 'factory_Update', params: { factory_Id: this.factoryID,factory_Name: this.factoryNAME,factory_Common_Name: this.factoryCommonNAME, }}">
                更新画面へ遷移(工場情報更新機能)</router-link>
            </v-container>
            <br><br>
        </v-main>
    </v-app>
</template>


<style type="text/css">
    p {
        font-family: 'Century', Times, serif;
        color:black;
    }
    .message {
        color: red;
    }
    .custom-drawer {
        background-color: white;
    }
    .select-Dropdown-v1 {
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: white;
        font-size: 18px;
        width: 90px;
    }
    .select-Dropdown-v2 {
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: white;
        font-size: 18px;
        width: 140px;
    }
    .select-Dropdown-v3 {
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: white;
        font-size: 18px;
        width: 30px;
    }
    td {
        text-align: center; 
    }
</style>

<script>
    import axios from 'axios';

    import InputField from '@/components/input_Field.vue';
    import select_Dropdown from '@/components/select_Dropdown.vue';

    export default {
        components: {
            InputField,
            select_Dropdown,
        },
        data() {
            return {
                factoryID: '',
                factoryNAME: '',
                factoryCommonNAME: '',
                selectClass: 'your-class',
                input_Emp_Search: '',
                Error: false,
                Message: '',
                items_Per_Page: 5,
                selected_Conditions: '完全一致',
                conditions: ['完全一致', '前方一致'],
                selected_Target: '正式名称',
                target: ['正式名称', '工場ID', '通称'],
                selected_Sort: '正式名称(昇順)',
                sort: ['正式名称(昇順)', '正式名称(降順)', '通称(昇順)', '通称(降順)'],
                selected_Factory: '-',
                options: ['○', '-'],
                headers: [
                    { text: '工場ID' },
                    { text: '正式名称' },
                    { text: '創立年月日' },
                    { text: '通称' },
                    { text: '創立周年' },
                    { text: '更新対象選択' },
                ],
                desserts: [],
            };
        },
        methods: {
            SEARCH() {
                let hasError = false;

                if (this.input_Emp_Search.length < 1) {
                    this.char_Error = true;
                    hasError = true;
                } else {
                    this.char_Error = false;
                }


                if (!hasError) {
                    let Conditions;
                    let Target;
                    let Sort;

                    if (this.selected_Conditions == '完全一致') {
                        Conditions = 'all';
                    } else {
                        Conditions = 'front';
                    }

                    


                    if (this.selected_Target == '正式名称') {
                        Target = 'factory_Name';
                    } else if (this.selected_Target == '工場ID') {
                        Target = 'factory_ID';
                    } else if (this.selected_Target == '通称') {
                        Target = 'factory_Common_Name';
                    }

                    if (this.selected_Sort == '正式名称(昇順)') {
                        Sort = 'factory_Name ASC';
                    } else if (this.selected_Sort == '正式名称(降順)') {
                        Sort = 'factory_Name DESC';
                    } else if (this.selected_Sort == '通称(昇順)') {
                        Sort = 'factory_Common_Name ASC';
                    } else if (this.selected_Sort == '通称(降順)') {
                        Sort = 'factory_Common_Name DESC';
                    }


                    axios
                        .request({
                            method: 'POST',
                            url: 'http://localhost:8080/factory_Search',
                            data: {
                                input_Emp_Search: this.input_Emp_Search,
                                conditions: Conditions,
                                target: Target,
                                sort: Sort,
                            }
                        })
                        .then((response) => {
                            console.log(response.data);
                            if (response.data == '') {
                                const Message = '検索結果が存在しません';
                                this.Message = Message;


                            } else {
                                this.desserts = response.data.map((item) => {
                                    const parts = item.split(", ");
                                    const factory_Name = parts[0].split("=")[1];
                                    const factory_Common_Name0 = parts[1].split("=")[1];
                                    const factory_Common_Name1 = parts[2].split("=")[1];
                                    const factory_Common_Name2 = parts[3].split("=")[1];
                                    const birth = parts[4].split("=")[1];
                                    const factory_ID = parts[5].split("=")[1];
                                    const age = parts[6].split("=")[1];

                                    const Message = '検索が完了しました';
                                    this.Message = Message;

                                    return {
                                        '工場ID': factory_ID,
                                        '正式名称': factory_Name,
                                        '創立年月日': birth,
                                        '通称': factory_Common_Name0 + '<br>' + factory_Common_Name1 + '<br>' + factory_Common_Name2,
                                        '創立周年': age,
                                        'selected_Factory': '-'

                                    };
                                });
                            }

                        })
                }
            },
            handleFactoryChange(item) {
                this.factoryID = item['工場ID'];
                this.factoryNAME = item['正式名称'];
                this.factoryCommonNAME = item['通称'];
            }
        },
    }

</script>