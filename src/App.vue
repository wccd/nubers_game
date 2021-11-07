<script setup>
import { ref } from 'vue'

const nums = ref([1,2,3,4,5,6,7,8])

class Block {
    label = ''
    id = -1
    x = 0
    y = 0
    nowPosition = 0
    targetPosition = 0
    
    constructor(obj) {
        this.label = obj.label;
        this.id = obj.id;
        this.nowPosition = obj.now;
        this.targetPosition = this.id;
        this.x = this.nowPosition % 3;
        this.y = Math.floor(this.nowPosition / 3);
    }

    isCorrect() {
        return this.nowPosition == this.targetPosition
    }

    move(x, y) {
        this.x = x;
        this.y = y;
        this.nowPosition = x + y * 3;
    }

    moveBlock(direct) {
        let temp = this.nowPosition;
        switch(direct) {
            case 2:
                // up
                this.move(this.x, this.y - 1);
                break
            case 3: 
                //right
                this.move(this.x + 1, this.y);
                break
            case 0: 
                //down
                this.move(this.x, this.y + 1);
                break
            case 1: 
                //left
                this.move(this.x - 1, this.y);
                break
        }
        return temp
    }

}
class Game {
    blockList = [];
    moving = false;
    nullIndex = 8;
    
    constructor() {
        let randomArr = this.randomBlock();
        for(let i = 0; i < 8; i++) {
            let block = new Block({
                id: randomArr[i],
                label: randomArr[i],
                now: i
            });
            this.blockList.push(block);
        }
        console.log(this.blockList)
    }

    shuffle(arr) {
        let len = arr.length - 1;
        while(len) {
            let target = Math.floor(Math.random() * len);
            [arr[target], arr[len]] = [arr[len], arr[target]];
            len--;
        }
        return arr
    }

    randomBlock() {
        let arr = [1,2,3,4,5,6,7,8];
        return this.shuffle(arr);
    }

    getActiveBlock() {
        let activeBlock = [];
        let x = this.nullIndex % 3,
            y = Math.floor(this.nullIndex / 3);
        
        let top = y == 0 ? -1 : x + (y - 1) * 3,
            right = x == 2 ? -1 : x + 1 + y * 3,
            bottom = y == 2 ? -1 : x + (y + 1) * 3,
            left = x == 0 ? -1 : x - 1 + y * 3;

        activeBlock = [top, right, bottom, left];
        return activeBlock
    }

    getIndexById(id) {
        for(let i = 0; i < this.blockList.length; i++) {
            if(id == this.blockList[i].id)
                return i
        }
    }

    moveBlock(id) {
        let arr = this.getActiveBlock(),
            index = this.getIndexById(id),
            temp = arr.indexOf(this.blockList[index].nowPosition);
        if(temp != -1) {
            this.nullIndex = this.blockList[index].moveBlock(temp);
            console.log('move')
        } else {
            console.log('not move')
        }
    }
}
let game = new Game();
nums.value = game.blockList

function moveBlock(index) {
    game.moveBlock(index)
    nums.value = []
    nums.value = game.blockList
}
</script>

<template>
    <div class="box">
        <div class="gray-line-h" style="left: 80px;"></div>
        <div class="gray-line-h" style="left: 160px;"></div>
        <div class="gray-line-v" style="top: 80px;"></div>
        <div class="gray-line-v" style="top: 160px;"></div>
        
        <div 
            class="item" 
            v-for="item in nums" 
            :key="item.id" 
            :style="`left:${item.x * 80}px;top:${item.y * 80}px`"
            @click="moveBlock(item.id)"
            >
            {{item.label}}
        </div>
    </div>
</template>

<style>
.box {
    position: relative;
    width: 240px;
    height: 240px;
    border: 1px solid #000;
    left: 100px;
    top: 100px;
}
.gray-line-h {
    position: absolute;
    height: 100%;
    width: 1px;
    background: #b0b0b0;
}
.gray-line-v {
    position: absolute;
    width: 100%;
    height: 1px;
    background: #b0b0b0;
}
.item {
    position: absolute;
    width: 80px;
    height: 80px;
    text-align: center;
    line-height: 80px;
    user-select: none;
    cursor: pointer;
}
</style>
