<template>
    <div class="relative p-2 max-h-screen" style="min-height:20rem;">
        <i v-if="!$attrs.embeded" class="material-icons absolute top-0 right-0 mt-1 mr-1 rounded-full cursor-pointer bg-red-500 text-white" @click="$emit('close')">highlight_off</i>
        <i class="material-icons text-sm nuxpresso-icon-btn text-black absolute top-0 right-0 m-1 mt-6 mr-10 cursor-pointer" title="add image" @click="addImage">image</i>
        
       
            <quill-editor
                v-if="hasContent" 
                style="min-height:20rem;"
                class="mt-2 bg-white overflow-y-auto"
                ref="editor"
                id="editor"
                v-model="content"
                :options="$attrs.mode? editorOptionsHeading : editorOptions"
                @input="$emit('input',content)"
                @blur="onEditorBlur($event)"
                @focus="onEditorFocus($event)"
                @ready="onEditorReady($event)"
            /> 
        
        <transition name="fade">
            <div v-if="media" class="fixed top-0 left-0 m-auto rounded-lg bg-white">
                <moka-media @close="media=false" @newimage="setEditorImage"/>
            </div>
        </transition>


    </div>
</template>

<script>
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { quillEditor } from 'vue-quill-editor'
import Quill from 'quill'
import ImageResize from 'quill-image-resize-module'
Quill.register('modules/imageResize', ImageResize)

import typo from '@/plugins/typo.js'

export default {
    name: 'MokaRichTextEditor',
    components:{
        quillEditor
    },
    data:()=>({
        editor: false,
        media: false,
        content: '',
        /*
        editorOptions: {
            modules: {
                imageResize: true,
                toolbar: {
                    container: [['bold', 'italic', 'underline', 'strike'],        // toggled buttons
                        ['blockquote', 'code-block'],

                        [{ 'header': 1 }, { 'header': 2 }],               // custom button values
                        [{ 'list': 'ordered'}, { 'list': 'bullet' }],
                        [{ 'script': 'sub'}, { 'script': 'super' }],      // superscript/subscript
                        [{ 'indent': '-1'}, { 'indent': '+1' }],          // outdent/indent
                        [{ 'direction': 'rtl' }],                         // text direction

                        [{ 'size': ['small', false, 'large', 'huge'] }],  // custom dropdown
                        [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
                        [{ 'color' : typo.colors },{ 'background' : typo.colors }],
                        //[{ 'color': [ '#000000', '#FFFFFF' , '#F7FAFC' , '#EDF2F7' , '#E2E8F0', '#CBD5E0', '#A0AEC0', '#718096','#4A5568' , '#2D3748', '#1A202C', '#FFF5F5', '#FED7D7', '#FEB2B2' , '#FC8181', '#F56565','#E53E3E', '#C53030', '#9B2C2C', '#742A2A', '#FFFAF0', '#FEEBC8','#FBD38D', '#F6AD55','#ED8936','#DD6B20','#C05621','#9C4221','#7B341E','#FFFFF0','#FEFCBF','#FAF089','#F6E05E','#ECC94B','#D69E2E','#B7791F','#975A16','#744210','#F0FFF4','#C6F6D5','#9AE6B4','#68D391','#48BB78','#38A169','#2F855A','#276749','#22543D','#E6FFFA','#B2F5EA','#81E6D9','#4FD1C5','#38B2AC','#319795','#2C7A7B','#285E61','#234E52','#EBF8FF','#BEE3F8','#90CDF4','#63B3ED','#4299E1','#3182CE','#2B6CB0','#2C5282','#2A4365','#EBF4FF','#C3DAFE','#A3BFFA','#7F9CF5','#667EEA','#5A67D8','#4C51BF','#434190','#3C366B','#FAF5FF','#E9D8FD','#D6BCFA','#B794F4','#9F7AEA','#805AD5','#6B46C1','#553C9A','#44337A','#FFF5F7','#FED7E2','#FBB6CE','#F687B3','#ED64A6','#D53F8C','#B83280','#97266D','#702459'] }, { 'background': [ '#000000', '#FFFFFF' , '#F7FAFC' , '#EDF2F7' , '#E2E8F0', '#CBD5E0', '#A0AEC0', '#718096','#4A5568' , '#2D3748', '#1A202C', '#FFF5F5', '#FED7D7', '#FEB2B2' , '#FC8181', '#F56565','#E53E3E', '#C53030', '#9B2C2C', '#742A2A', '#FFFAF0', '#FEEBC8','#FBD38D', '#F6AD55','#ED8936','#DD6B20','#C05621','#9C4221','#7B341E','#FFFFF0','#FEFCBF','#FAF089','#F6E05E','#ECC94B','#D69E2E','#B7791F','#975A16','#744210','#F0FFF4','#C6F6D5','#9AE6B4','#68D391','#48BB78','#38A169','#2F855A','#276749','#22543D','#E6FFFA','#B2F5EA','#81E6D9','#4FD1C5','#38B2AC','#319795','#2C7A7B','#285E61','#234E52','#EBF8FF','#BEE3F8','#90CDF4','#63B3ED','#4299E1','#3182CE','#2B6CB0','#2C5282','#2A4365','#EBF4FF','#C3DAFE','#A3BFFA','#7F9CF5','#667EEA','#5A67D8','#4C51BF','#434190','#3C366B','#FAF5FF','#E9D8FD','#D6BCFA','#B794F4','#9F7AEA','#805AD5','#6B46C1','#553C9A','#44337A','#FFF5F7','#FED7E2','#FBB6CE','#F687B3','#ED64A6','#D53F8C','#B83280','#97266D','#702459'] }],          // dropdown with defaults from theme
                        //[{ 'font': ['Barlow Condensed','Abel','Alice','Alegreya','Arial','Amethysta','Nunito Sans','Roboto','Quattrocento','Verdana','sans-serif','serif'] }],
                        [{ 'align': [] }],
                        
                        ['clean']    
                    ],
                    
                },
                
                
            },
            placeholder: 'Add your content ...',
            theme: 'snow'
        },
        */
        editorOptionsHeading: {
            modules: {
                imageResize: true,
                toolbar: {
                    container : [
                        [{ 'header': 1 }, { 'header': 2 }],               // custom button values
                        [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
                        [{ 'color': [] }, { 'background': [] }],          // dropdown with defaults from theme
                        //[{ 'font': ['Barlow Condensed','Arial','Verdana','sans-serif','serif'] }],
                        [{ 'align': [] }],
                        ['clean']    
                    ],
                },
            },
            placeholder: 'Add your content ...',
            theme: 'snow'
        }
    }),
    computed:{
        hasContent(){
            this.$attrs.value ? this.content = this.$attrs.value : null
            return true
        },
        editorOptions(){
            return { modules: {
                imageResize: true,
                toolbar: {
                    container: [['bold', 'italic', 'underline', 'strike'],        // toggled buttons
                        ['blockquote', 'code-block'],
                        [{ 'header': 1 }, { 'header': 2 }],               // custom button values
                        [{ 'list': 'ordered'}, { 'list': 'bullet' }],
                        [{ 'script': 'sub'}, { 'script': 'super' }],      // superscript/subscript
                        [{ 'indent': '-1'}, { 'indent': '+1' }],          // outdent/indent
                        [{ 'direction': 'rtl' }],                         // text direction
                        [{ 'size': ['small', false, 'large', 'huge'] }],  // custom dropdown
                        [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
                        [{ 'color' : typo.typo.colors },{ 'background' : typo.typo.colors }],
                        [{ 'align': [] }],
                        ['clean']    
                    ],
                    
                },
                
                
            },
            placeholder: 'Add your content ...',
            theme: 'snow'}
        }
    },
    methods: {
        addImage(){
            this.media = true 
        },
        onEditorBlur(editor) {
            return null
        },
        onEditorFocus(editor) {
            return null
        },
        onEditorReady(editor) {
            return null
        },
        setEditorImage(img){
                this.$refs['editor'].quill.focus()
                let range = this.$refs['editor'].quill.getSelection();
                range ? 
                    this.$refs['editor'].quill.insertEmbed(range.index, 'image', img.url ) :
                        this.$emit('message','Set a position in the editor to place the image')
        }, 
    },
    beforeMount(){
        const Quill = require("quill");
        const {
            container,
            ImageExtend,
            QuillWatch
        } = require("quill-image-extend-module");

        const { ImageDrop } = require("quill-image-drop-module");

        const ImageResize = require("quill-image-resize-module");

        Quill.register("modules/ImageExtend", ImageExtend);
        Quill.register("modules/ImageResize", ImageResize);
        Quill.register("modules/imageDrop", ImageDrop);

    }
}
</script>

<style>
.ql-container {
    font-family: 'Arial';
}
.ql-editor {
    overflow-y: auto;
    max-height:30rem;
}
</style>