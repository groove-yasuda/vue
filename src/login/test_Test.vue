<template>
    <v-app>
        <v-main>
            <v-container>

                <p>社員情報登録</p>
                <p>社員情報入力画面</p>


                <br>
                <v-row justify="center">
                    <p>社員情報入力画面</p>
                </v-row>
                <br><br>

                <v-row justify="center">
                    <v-col cols="auto">
                        <v-btn v-on:click="generateRandomInteger">登録</v-btn>
                    </v-col>

                    <v-col cols="auto">
                        <v-btn v-on:click="generateRandomInteger">登録</v-btn>
                    </v-col>

                    <v-col cols="auto">
                        <v-btn v-on:click="generateRandomInteger">登録</v-btn>
                    </v-col>
                </v-row>


            </v-container>



        </v-main>
    </v-app>
</template>



<style>
    .a {
        width: 80%;
        color: red;
    }

    .error-message {
        color: red;
    }
</style>

<script>
    export default {
        data() {
            return {
                randomInteger: null,
            };
        },
        methods: {
            generateRandomInteger() {
                // Math.random()で0以上1未満の浮動小数点数を生成
                const randomFloat = Math.random();

                // 0, 1, 2のいずれかの整数に変換
                // Math.floor()を使用して切り捨て
                this.randomInteger = Math.floor(randomFloat * 3);
            },
        },
    };
</script>

