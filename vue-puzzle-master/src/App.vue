<template>
<div class="box">
    <div id="note">
        <p id="myTime">时间 {{time}}</p>
        <p id="myTime">步数 {{step}}</p>
        <input type="text" v-model="tmp">
        <button @click="setHard">难度系数确认</button>
    </div>  
    <div class="center">
        <ul id="puzzle-wrap">
            <li 
                :class="{'puzzle': true, 'puzzle-empty': !puzzle}" 
                v-for="puzzle in puzzles" 
                v-text="puzzle"
                @click="moveFn($index)"
                :style="'width:'+1/size*100+'%;height:'+1/size*100+'%;line-height:'+400/size+'px;'"
            ></li>
        </ul>
        <button id="start" class="btn btn-warning btn-block btn-start" @click="start" >开始游戏</button>
        <button id="pause" class="btn btn-warning btn-block btn-start" @click="pause">暂停游戏</button>
        <button id="continue" class="btn btn-warning btn-block btn-reset" @click="continue">继续游戏</button>
    </div>
</div>
</template>

<script>
export default {
        
    data () {
        return {
            time: "00:00",
            puzzles: [],
            timer: null,
            n:0,
            step:0,
            size:4,
            tmp:4
        }
    },
    methods: {
        setHard(){
            this.size=this.tmp;
            this.setPuzzles();
        },
        
        start(){
            var oStart=document.getElementById("start");
            oStart.innerHTML="重置游戏";
            this.render ();
            this.clock();
        },
        pause(){
            clearInterval(this.timer);
            var oDiv=document.getElementById("puzzle-wrap");
            oDiv.style.visibility="hidden";
            var oPause=document.getElementById("pause");
            oPause.style.display="none";
            var oContinue=document.getElementById("continue");
            oContinue.style.display="inline";
        },
        continue(){
            this.clock();
            var oDiv=document.getElementById("puzzle-wrap");
            oDiv.style.visibility="visible";
            var oPause=document.getElementById("pause");
            oPause.style.display="inline";
            var oContinue=document.getElementById("continue");
            oContinue.style.display="none";
        },
        setDisplay(obj,name,act){
            var oBtn=document.getElementById(obj);
            oBtn.style[name]=act;
        },

        // 重置渲染
        render () {
            clearInterval(this.timer);
            this.time="00:00";
            this.n=0;
            this.step=0;
            
            let puzzleArr = [],
                i = 1

            // 生成包含1 ~ 15数字的数组
            for (i; i < this.size*this.size; i++) {
                puzzleArr.push(i)
            }

            // 随机打乱数组
            puzzleArr = puzzleArr.sort(() => {
                return Math.random() - 0.5
            });

            // 页面显示
            this.puzzles = puzzleArr
            this.puzzles.push('')
        },
        clock(){
            clearInterval(this.timer);
            function toDub(n){
                return {
                n:n<10?("0"+n):(""+n)
                }
            }   
            this.timer=setInterval(() => {
                this.n++;
                var m=parseInt(this.n/60)
                var s=parseInt(this.n%60);
                var mm=new toDub(m);
                var ss=new toDub(s);
                this.time=mm.n+":"+ss.n;
            },1000)
        },
        // 点击方块
        moveFn (index) {

            // 获取点击位置及其上下左右的值
            let curNum = this.puzzles[index],
                leftNum = this.puzzles[index - 1],
                rightNum = this.puzzles[index + 1],
                topNum = this.puzzles[index - this.size],
                bottomNum = this.puzzles[index + this.size]

            // 和为空的位置交换数值
            if (leftNum === '' && index % this.size) {
                this.puzzles.$set(index - 1, curNum)
                this.puzzles.$set(index, '')
                this.step++;
            } else if (rightNum === '' && (this.size-1) !== index % this.size) {
                this.puzzles.$set(index + 1, curNum)
                this.puzzles.$set(index, '')
                this.step++;
            } else if (topNum === '') {
                this.puzzles.$set(index - this.size, curNum)
                this.puzzles.$set(index, '')
                this.step++;
            } else if (bottomNum === '') {
                this.puzzles.$set(index + this.size, curNum)
                this.puzzles.$set(index, '')
                this.step++;
            }

            this.passFn()
        },

        // 校验是否过关
        passFn () {
            if (this.puzzles[this.size*this.size-1] === '') {
                var newPuzzles = this.puzzles.slice(0, this.size*this.size-1)

                var isPass = newPuzzles.every((e, i) => e === i + 1)

                if (isPass) {
                    alert ('恭喜，闯关成功！')
                }
            }
        },
        setPuzzles(){
            let puzzleArr = [],
                    i = 1

            // 生成包含1 ~ 15数字的数组
            for (i; i < this.size*this.size; i++) {
                puzzleArr.push(i)
            }
            this.puzzles = puzzleArr
            this.puzzles.push('')
        }
    },
    ready () {
        this.setPuzzles()
    }
}
</script>

<style>
@import url('./assets/css/bootstrap.min.css');

body {
    font-family: Arial, "Microsoft YaHei"; 
}

.box {
    width: 400px;
    margin: 30px auto 0;
    height:100%;
}
.center{
    position:relative;
    top:20px;
    background:#ccc;
}
#note {
    position:relative;
    border:1px solide black;
    top:50px;
}
#puzzle-wrap {
    width: 400px;
    height: 400px;
    margin-bottom: 40px;
    padding: 0;
    background: #ccc;
    list-style: none;
}

.puzzle {
    float: left;
    font-size: 20px;
    background: #f90;
    text-align: center;
    border: 1px solid #ccc;
    box-shadow: 1px 1px 4px;
    text-shadow: 1px 1px 1px #B9B4B4;
    cursor: pointer;
}

.puzzle-empty {
    background: #ccc;
    box-shadow: inset 2px 2px 18px;
}

.btn-start {
    box-shadow: inset 2px 2px 18px;
}
.btn-reset {
    box-shadow: inset 2px 2px 18px;
    display:none;
}

#note input{
    width: 50px;
    height: 20px;
    right:120px;
    position: absolute;
}
#note button{
    width: 120px;
    height: 20px;
    right:0px;
    position: absolute;
}
#note p{
    margin-left: 0px;
    width: 80px;
    height: 50px;
    border:none;
    display: inline-block;

}
</style>