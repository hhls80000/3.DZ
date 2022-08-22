<template>
    <el-dialog :title="title" :visible.sync="dialogVisible" width="30%" @close="changeClose" center>
        <keep-alive> <component :is="assembly" /> </keep-alive>
        <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">关闭</el-button>
        </span>
    </el-dialog>
</template>

<script>
import LmgList from './ImgList.vue';
import HandleMsg from './HandleMsg.vue';
import ContentDetails from './ContentDetails.vue';
import LookMsgList from './LookMsgList.vue';
export default {
    name: 'Dialog',
    components: {
        LmgList,
        HandleMsg,
        ContentDetails,
        LookMsgList
    },
    props: {
        dialogShow: {
            type: Boolean,
            default: false
        },
        dialogTitle: {
            type: String,
            default: ''
        },
        dialogIs: {
            type: String,
            default: ''
        }
    },
    data() {
        return {
            dialogVisible: this.dialogShow,
            assembly: this.dialogIs,
            title: this.dialogTitle
        };
    },
    watch: {
        dialogShow(val) {
            this.dialogVisible = val;
        },
        dialogTitle(val) {
            console.log(val);
            this.title = val;
        },
        dialogIs(val) {
            this.assembly = val;
        }
    },

    created() {},
    methods: {
        getUrlList(type) {
            console.log(type);
            switch (type) {
                case type == '附件':
                    this.assembly = 'LmgList';
                    break;
                case type == '处理':
                    this.assembly = 'HandleMsg';
                    break;
                case type == '问题描述':
                    this.assembly = 'ContentDetails';
                    break;
                case type == '查看消息':
                    this.assembly = 'LookMsgList';
                    break;
                default:
                    break;
            }
        },
        changeClose() {
            this.$emit('dialogClose', false);
        }
    }
};
</script>

<style scoped></style>
