<template>
    <div class="home">
        <div v-if="!trained">
            <p>
                x:  <input type="number" v-model="xs">
                y:  <input type="number" v-model="ys">
                <button @click="onAdd($event)">add</button>
            </p>
            <p v-for="(item, index) in dataset" :key="index">
                x:{{item.x}}, y:{{item.y}} <button @click="remove(index)">remove</button>
            </p>
            <p v-if="dataset.length > 2">
                <button @click="onTrainModel()">开始训练</button>
            </p>
        </div>
        <div v-else>
            <p>Trained! Now fill the X to predict the Y</p>
            <p><input type="number" v-model="xx"> <button @click="predict()">预测</button></p>
            <p v-if="predictY">预测Y值为：{{predictY}}</p>
        </div>
    </div>
</template>

<script>
    import LinearModel from '@/util/LinearModel'
    import uuid from 'uuid';

    export default {
        name: 'home',
        data() {
            return {
                linearModel: null,
                xs: '',
                ys: '',
                dataset: [], // 训练数据
                trained: false, // 训练状态

                xx: undefined, // 预测输入的X值
                predictY: undefined, // 预测的Y值
            }
        },
        created() {
            this.linearModel = new LinearModel();
            // let newValues = null;
            // for(let i = 0; i < 100; i++) {
            //     newValues = { id: uuid(), x: i, y: i };
            //     this.dataset.push(newValues);
            // }
        },
        methods: {
            // 预测
            predict() {
                const input = parseFloat(this.xx);

                if(!this.isValid(input)){
                    alert({ error: 'Input from prediction is not a valid number'});
                    return;
                }

                this.predictY = this.linearModel.predict(input);
            },
            // 添加数据
            onAdd(event){
                const x = parseFloat(this.xs);
                const y = parseFloat(this.ys);

                event.preventDefault();

                if(!this.isValid(x)){
                    alert({ error: 'X is not a valid number'});
                    return;
                }

                if(!this.isValid(y)){
                    alert({ error: 'Y is not a valid number'});
                    return;
                }
                const newValues = { id: uuid(), x, y };
                this.xs = undefined;
                this.ys = undefined;

                this.dataset = [...this.dataset, newValues];
            },
            remove(index) {
                this.dataset.splice(index, 1);
            },
            isValid(num) {
                return typeof num === 'number' && !Number.isNaN(num);
            },
            // 开始训练
            onTrainModel(){
                const xs = this.dataset.map(data => data.x);
                const ys = this.dataset.map(data => data.y);

                this.linearModel.trainModel(xs, ys).then(() => {
                    this.trained = true;
                });
            }
        },
    }
</script>
