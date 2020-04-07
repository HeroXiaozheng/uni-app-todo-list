<template>
    <view class="todo_wrapper">
        <uni-nav-bar :shadow="false">
            <view slot="default" class="nav__title">
                <text>待办事项</text>
            </view>
            <view slot="right">
                <uni-icons type="plusempty"
                    color="#ccc"
                    size="28"
                    @click="$refs.addDialog.open()">
                </uni-icons>
            </view>
        </uni-nav-bar>
        <uni-swipe-action class="todo__list" 
            v-for="(item, index) in todoDataList"
            :key="index">
            <uni-swipe-action-item :options="options"
                @click="handleTodoList($event, index)"
                :autoClose="true">
                <view class="todo__list-cell">
                    <view class="todo__list__header">
                        <view class="todo__list-title">
                            {{ item.title }}
                        </view>
                        <view class="todo__list-important">
                            <image :src="showImportant(item.important)"
                                class="todo__list-important-icon" >
                            </image>
                        </view>
                    </view>
                    <view class="todo__list-start-time">
                        创建于{{ new Date(item.startTime).toLocaleString() }}
                    </view>
                </view>
            </uni-swipe-action-item>
        </uni-swipe-action>
        <uni-popup ref="deleteDialog"
            :maskClick="false">
            <view class="todo__list-delete-dialog__wrapper">
                <view class="todo__list-delete-dialog__body">
                    确定要删除吗？
                </view>
                <view class="todo__list-delete-dialog__bottom">
                    <text class="todo__list-delete-dialog__bottom-btn border-right"
                        @tap="$refs.deleteDialog.close()">
                        取消
                    </text>
                    <text class="todo__list-delete-dialog__bottom-btn warn-opt"
                        @tap="deleteToDoList">
                        删除
                    </text>
                </view>
            </view>
        </uni-popup>

        <!-- 待办事项输入框 -->
        <uni-popup ref="addDialog"
            :maskClick="false">
            <view class="todo__list-delete-dialog__wrapper">
                <view class="todo__list-delete-dialog__title">
                    添加待办事项
                </view>
                <view class="todo__list-delete-dialog__body_input">
                    <text class="input_text">内容：</text>
                    <input type="text"
                        :focus="true"
                        class="input_area"
                        v-model="todoDetail">
                </view>
                <view class="todo__list-delete-dialog__body_input">
                    <text class="input_text">重要程度：</text>
                    <radio-group class="radio_area" @change="radioChange">
                        <label class="radio_area radio_label" v-for="(item, index) in importantLevel"
                            :key="index">
                            <view class="radio_single">
                                <radio :value="item.value" :checked="item.default"></radio>
                            </view>
                            <view class="radio_text">
                                {{ item.text }}
                            </view>
                        </label>
                    </radio-group>
                </view>
                <view class="todo__list-delete-dialog__bottom">
                    <text class="todo__list-delete-dialog__bottom-btn border-right"
                        @tap="$refs.addDialog.close()">
                        取消
                    </text>
                    <text class="todo__list-delete-dialog__bottom-btn save-opt"
                        @tap="addTodoList">
                        保存
                    </text>
                </view>
            </view>
        </uni-popup>
    </view>
</template>

<script>
const operation = {
    'FINISHED': '标记为完成',
    'DELETE': '删除'
};
import todoData from '../../../data/todoData';
import doingData from '../../../data/doingData';
import {uniSwipeAction, uniSwipeActionItem, uniPopup, uniNavBar, uniIcons} from '@dcloudio/uni-ui';
export default {
    data () {
        return {
            todoDataList: todoData,
            contentVisible: false,
            options: [
                {   
                    type: '',
                    text: operation.FINISHED,
                    style: {
                        backgroundColor: '#ccc'
                    }
                },
                {
                    text: operation.DELETE,
                    style: {
                        backgroundColor: '#dd524d'
                    }
                },
            ],
            deleteObj: 0,
            todoDetail: '',
            todoImportant: 'high',
            importantLevel: [
                {
                    text: '高',
                    value: 'high',
                    default: true
                },
                {
                    text: '中',
                    value: 'middle'
                },
                {
                    text: '低',
                    value: 'low'
                }
            ]
        }
    },
    components: {
        uniSwipeAction,
        uniSwipeActionItem,
        uniPopup,
        uniNavBar,
        uniIcons
    },
    computed: {
        showImportant (important) {
            return (important) => {
                return `../../static/${important}.svg`;
            };
        }
    },  
    onLoad: () => {
        
    },
    methods: {
        handleTodoList (e, index) {
            let opt = e.content.text;
            if (opt === operation.DELETE) {
                this.$refs.deleteDialog.open();
                this.deleteObj = index;
            } else {
                this.markFinished(this.todoDataList[index]);
                this.todoDataList.splice(index, 1);
            }
        },
        deleteToDoList () {
            this.todoDataList.splice(this.deleteObj, 1);
            this.$refs.deleteDialog.close();
        },
        markFinished (item) {
            doingData.push(item);
        },
        addTodoList () {
            let newTodoData = {
                'id': 'todo001',
                'title': this.todoDetail,
                'startTime': new Date().getTime(),
                'important': this.todoImportant,
                'type': '工作',
                'state': '0'
            };
            todoData.push(newTodoData);
            this.$refs.addDialog.close();
            this.todoDetail = '';
            this.todoImportant = '';
        }, 
        radioChange (e) {
            this.todoImportant = e.detail.value;
        }
    }
}
</script>

<style>
.todo__list {
    flex-direction: column;
}
.todo__list-cell {
    width: 100%;
    padding: 20rpx 20rpx 10rpx;
    border-bottom: 1rpx solid #eee;
}
.todo__list__header {
    flex-direction: row;
}
.todo__list-title {
    font-size: 30rpx;
    flex-grow: 1;
}
.todo__list-important-icon {
    width: 20rpx;
    height: 20rpx;
    position: relative;
    top: 5px;
}
.todo__list-start-time {
    font-size: 16rpx;
    color: #ccc;
    margin-top: 5rpx;
}
.todo__list-content {
    font-size: 25rpx;
    margin-top: 20rpx;
}
.todo__list-delete-dialog__wrapper {
    background: #fff;
    width: 620rpx;
    border-radius: 19rpx;
}
.todo__list-delete-dialog__body {
    font-size: 16px;
    color: #14161A;
    text-align: center;
    line-height: 20px;
    margin: 68rpx 0;
}
.todo__list-delete-dialog__bottom {
    height: 96rpx;
    display: flex;
    flex-direction: row;
    border-top: 2rpx solid #EBEDF0;
}
.todo__list-delete-dialog__bottom-btn {
    flex-grow: 1;
    text-align: center;
    line-height: 96rpx;
    font-size: 16px;
    color: #333333;
}
.border-right {
    border-right: 1rpx solid #EBEDF0;
}
.warn-opt {
    color: #DE1B1B;
}
.save-opt {
    color: #3366FF;
}
.nav__title {
    flex-basis: 100%;
    text-align: center;
}
.todo__list-delete-dialog__title {
    font-family: PingFangSC-Medium;
    font-size: 18px;
    color: #14161A;
    text-align: center;
    line-height: 40rpx;
    margin-top: 48rpx;
}
.todo__list-delete-dialog__body_input {
    display: flex;
    padding: 0 40rpx;
    margin: 40rpx 0;
    flex-direction: row;
}
.input_text {
    font-size: 25rpx;
    flex-basis: 150rpx;
}
.input_area {
    border-bottom: 1rpx solid #ccc;
    flex: 1;
}
.radio_area {
    display: flex;
    flex-direction: row;
    position: relative;
    bottom: 4rpx;
}
.radio_label {
    padding-left: 20rpx;
    font-size: 25rpx;
}
.radio_single {
    transform: scale(0.8);
}
.radio_text {
    position: relative;
    top: 4rpx;
}
.uni-navbar__content_view {
    justify-content: center;
}
</style>