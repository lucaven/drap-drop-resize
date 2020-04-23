<template>
    <div id="app">
        <h1 style="text-align: center">Drag-Drop-Resize</h1>
        <div id="content">
            <div style="margin-top: 10px;margin-bottom: 10px;">
                Col nums: <input type="number" v-model="colNum"/>
            </div>
            <grid-layout
                    :layout.sync="layout"
                    :col-num="parseInt(colNum)"
                    :row-height="20"
                    :is-draggable="draggable"
                    :is-resizable="resizable"
                    :is-mirrored="mirrored"
                    :prevent-collision="preventCollision"
                    :vertical-compact="true"
                    :use-css-transforms="true"
                    :responsive="responsive"
                    @layout-created="layoutCreatedEvent"
                    @layout-before-mount="layoutBeforeMountEvent"
                    @layout-mounted="layoutMountedEvent"
                    @layout-ready="layoutReadyEvent"
                    @layout-updated="layoutUpdatedEvent"
            >
                <grid-item v-for="item in layout" :key="item.i"
                           :static="item.static"
                           :x="item.x"
                           :y="item.y"
                           :w="item.w"
                           :h="item.h"
                           :i="item.i"
                           @resize="resize"
                           @move="move"
                           @resized="resized"
                           @container-resized="containerResized"
                           @moved="moved"
                >
                    <test-element :text="item.i"></test-element>
                </grid-item>
            </grid-layout>
            <hr/>
        </div>
    </div>
</template>

<script>
    import GridItem from './components/GridItem.vue';
    import GridLayout from './components/GridLayout.vue';
    import TestElement from './components/TestElement.vue';
    import CustomDragElement from './components/CustomDragElement.vue';
    import {getDocumentDir, setDocumentDir} from "./helpers/DOM";

    let testLayout = [
        {"x":0,"y":0,"w":1,"h":2,"i":"0", resizable: true, draggable: true, static: false},
        {"x":1,"y":0,"w":3,"h":2,"i":"1", resizable: null, draggable: null, static: false},
        {"x":4,"y":0,"w":2,"h":2,"i":"2", resizable: false, draggable: false, static: false},
        {"x":6,"y":0,"w":4,"h":2,"i":"3", resizable: false, draggable: false, static: false},
        {"x":10,"y":0,"w":2,"h":2,"i":"4", resizable: false, draggable: false, static: false},
    ];

    export default {
        name: 'app',
        components: {
            GridLayout,
            GridItem,
            TestElement,
            CustomDragElement,
        },
        data () {
            return {
                layout: JSON.parse(JSON.stringify(testLayout)),
                layout2: JSON.parse(JSON.stringify(testLayout)),
                draggable: true,
                resizable: true,
                mirrored: false,
                responsive: true,
                preventCollision: false,
                rowHeight: 30,
                colNum: 12,
                index: 0
            }
        },
        mounted: function () {
            this.index = this.layout.length;
        },
        methods: {
            clicked: function() {
                window.alert("CLICK!");
            },
            increaseWidth: function() {
                let width = document.getElementById("content").offsetWidth;
                width += 20;
                document.getElementById("content").style.width = width+"px";
            },
            decreaseWidth: function() {
                let width = document.getElementById("content").offsetWidth;
                width -= 20;
                document.getElementById("content").style.width = width+"px";
            },
            removeItem: function(item) {
                this.layout.splice(this.layout.indexOf(item), 1);
            },
            addItem: function() {
                let item = {"x":0,"y":0,"w":2,"h":2,"i":this.index+"", whatever: "bbb"};
                this.index++;
                this.layout.push(item);
            },
            move: function(i, newX, newY){
                console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
            },
            resize: function(i, newH, newW, newHPx, newWPx){
                console.log("RESIZE i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx);
            },
            moved: function(i, newX, newY){
                console.log("### MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
            },
            resized: function(i, newH, newW, newHPx, newWPx){
                console.log("### RESIZED i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx);
            },
            containerResized: function(i, newH, newW, newHPx, newWPx){
                console.log("### CONTAINER RESIZED i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx);
            },
            /**
             * Add change direction button
             */
            changeDirection() {
                let documentDirection = getDocumentDir();
                let toggle = "";
                if (documentDirection === "rtl") {
                    toggle = "ltr"
                } else {
                    toggle = "rtl"
                }
                setDocumentDir(toggle);
                //eventBus.$emit('directionchange');
            },

            layoutCreatedEvent: function(newLayout){
                console.log("Created layout: ", newLayout)
            },
            layoutBeforeMountEvent: function(newLayout){
                console.log("beforeMount layout: ", newLayout)
            },
            layoutMountedEvent: function(newLayout){
                console.log("Mounted layout: ", newLayout)
            },
            layoutReadyEvent: function(newLayout){
                console.log("Ready layout: ", newLayout)
            },
            layoutUpdatedEvent: function(newLayout){
                console.log("Updated layout: ", newLayout)
            },

        },
    }
</script>

<style>

</style>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
