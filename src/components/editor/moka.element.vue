<template>
    <div v-if="element" :ref="element.id" >
            <a v-if="el.hasOwnProperty('anchor')" :id="el.anchor"></a>

            <component :class="$cssResponsive(el.css)" :is="tag" v-html="el.content" v-if="(el.tag==='element' || el.type==='button')  && el.element !='img' && el.type != 'video' && el.type != 'audio' && !el.link" :style="stile"></component>
            <a v-if="el.link" :href="el.link">
                <component :class="$cssResponsive(el.css)" :is="tag" v-html="el.content" v-if="(el.tag==='element' || el.type==='button')  && el.element !='img' && el.type != 'video' && el.type != 'audio'" :style="stile"></component>
            </a>
            <component :class="$cssResponsive(el.css)" :is="tag" v-if="el.tag === 'article'" v-html="el.content"/>

            <svg v-if="el.tag === 'svg'" width="100%" height="100%" :viewBox="el.content.viewbox" v-html="el.content.g" :class="el.css + ' fill-current'"></svg>

            <!--<component :ref="element.id" :class="$cssResponsive(el.css)" :is="tag" v-if="el.type === 'video'" :src="el.src + el.content"/>-->
            

            <img v-if="el.element === 'img' && el.image && el.image.url" :src="el.image.url" :caption="el.image.caption" :alt="el.image.alternative_text" :class="$cssResponsive(el.css)"/>
            
            <video  :class="$cssResponsive(el.css)" v-if="el.type==='video' && el.image && el.image.url" autoplay controls>
                <source :src="el.image.url">
            </video>

            <!--<img :ref="element.id" v-if="el.element === 'img' && !el.image" src="../assets/no-image.png" :class="$cssResponsive(el.css)"/>-->
            
            <input :type="el.type" v-if="el.tag === 'input' && el.type!='button'" :class="$cssResponsive(el.css)" :value="el.content" :placeholder="el.required?'required!':''"/><sup v-if="el.required" class="ml-1 nuxpresso-element-required">*</sup>

            <!-- icon -->
            <i v-if="el.tag==='icon' && !el.link" :class="'material-icons moka-icons ' + $cssResponsive(el.css)">{{el.content}}</i>

            <a v-if="el.link && el.tag==='icon'" :href="el.link">
                <i v-if="el.tag==='icon'" :class="'material-icons moka-icons ' + $cssResponsive(el.css)">{{el.content}}</i>
            </a>
            <textarea v-if="el.element === 'textarea'" :class="$cssResponsive(el.css)"></textarea>

            <nav v-if="el.element === 'menu'" :class="menu_responsive(el) + ' ' + el.css.align"> 
                <div v-for="(item,i) in el.items" :class="el.css.css + ' cursor-pointer relative pr-4'" :key="el.id + '_' + i"> 
                    

                    <a :class="el.css.css" v-if="!item.submenu && !$attrs.develop && item.link && !item.link.includes('http')" :href="item.link">{{ item.label }} <i v-if="item.submenu" class="material-icons moka-icons">arrow_drop_down</i></a>
                    
                    <div v-else @mouseover="menuover=i" :class="el.css.css" @click="menuover=i">{{item.label}} <i v-if="item.submenu && item.submenu.length" :class="el.css.css + ' material-icons moka-icons text-sm'">arrow_drop_down</i></div>
                    
                    <div v-if="item.submenu && item.submenu.length" :class="isOver(i) + ' ' + el.css.css + ' absolute w-48 p-1 flex flex-col z-max'" @mouseleave="menuover=-1"> 
                        <div v-for="sub in item.submenu">
                            <div :class="el.css.css">{{sub.label}}</div>
                        </div>
                    </div>
                </div>
            </nav>
            <i :class="'material-icons moka-icons z-max fixed md:hidden top-0 left-0 m-1 ' + el.css.css" v-if="el.element === 'menu' && el.responsive" @click="menu_show=!menu_show">menu</i>
            <transition name="fade">
            <nav v-if="el.element === 'menu' && menu_show" class="flex flex-col p-1 my-2"> 
                <div v-for="(item,i) in el.items" :class="el.css.css + ' cursor-pointer relative p-1'"> 
                    
                    <a :class="el.css.css" v-if="!item.submenu && !$attrs.develop && item.link && !item.link.includes('http')" :href="item.link">{{ item.label }} <i v-if="item.submenu" class="material-icons moka-icons">arrow_drop_down</i></a>
                    
                    <div v-else @mouseover="menuover=i" :class="el.css.css" @click="menuover=i">{{item.label}} <i v-if="item.submenu && item.submenu.length" :class="el.css.css + ' material-icons moka-icons text-sm'">arrow_drop_down</i></div>
                    
                    <div v-if="item.submenu && item.submenu.length" :class="isOver(i) + ' ' + el.css.css + ' absolute w-48 p-1 flex flex-col z-40'" @mouseleave="menuover=-1"> 
                        <div v-for="sub in item.submenu">
                            <div :class="el.css.css">{{sub.label}}</div>
                        </div>
                    </div>
                </div>
            </nav>
            </transition>
    </div>
</template>

<script>
import { mapState } from 'vuex'
import gallery from '@/components/moka/moka.gallery'
 
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
gsap.registerPlugin ( ScrollTrigger )
const plugins = [ScrollTrigger];

export default {
    data:()=>({
        el: null,
        article: 'article',
        opacity: 'opacity-0',
        menuover: -1,
        menu_show: false,
    }),
    props: ['current'],
    beforeMount(){
        let vm = this
        this.article = 'article[' + this.$attrs.element.field + ']'
        if ( this.$attrs.element.element === 'article' && this.$attrs.element.id ){
            this.$axios.$get ( 'articles/' + this.$attrs.element.id ).then ( response => {
                vm.article = response[this.$attrs.element.field]
                return Promise.resolve(response)
            })
        }
    },
    computed:{
        ...mapState ( ['moka'] ),
        element(){
            return this.$attrs.element  ? this.el = this.$attrs.element : false
        },
        tag(){
            return this.$attrs.element.element === 'h' ? 'h' + this.$attrs.element.level : 
                    this.$attrs.element.element
        },
        stile(){
            if (this.el.image ){
                return 'background-image:url(' + this.el.image.url + ');background-repeat:no-repeat; background-size:cover;background-position:center center; ' + this.el.style  
            }
            return ''
        },
        
        animations(){
            return gsapEffects
        }
        
    },
    methods:{
        animation(element,id){
            let vm = this
            if ( this.$refs && element.hasOwnProperty('gsap') && element.gsap.animation ){
                let ani = gsap.effects[element.gsap.animation]( this.$refs[id] ,{
                    trigger: this.$refs[id],
                    duration: parseFloat(element.gsap.duration),
                    delay: parseFloat(element.gsap.delay),
                    ease: element.gsap.ease
                })

                ScrollTrigger.create({
                    id: id,
                    start: "top 80%",
                    trigger: this.$refs[id],
                    toggleActions: "play pause restart none",
                    animation:ani,
                    onEnter: ()=>{ 
                        if ( element.gsap.delay ){
                            ani.play()
                        } else {
                            ani.play(0)
                        }
                    },
                    
                });
                    
            }
        },
        responsiveCss(css){
            return this.$clean ( this.$cssResponsive ( css ) )
        },
        isOver(i){
            return i < 0 ? 'opacity-0' : this.menuover === i ? 'opacity-100' : 'opacity-0'
        },
        menu_responsive(menu){
            if ( menu.type === 'horizontal' && menu.responsive ) return 'hidden flex flex-col md:flex md:flex-row' 
            if ( menu.type === 'horizontal' && !menu.responsive ) return menu.css.container
            if ( menu.type === 'vertical' ) return 'flex flex-col'
        }
    },
    mounted(){
        if ( this.$attrs.sub ){
            console.log ( 'is sub' )
            this.animation(this.element,this.element.id)
        }
    }
}
</script>   