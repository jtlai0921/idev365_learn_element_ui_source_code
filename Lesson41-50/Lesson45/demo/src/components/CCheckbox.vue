<template>
    <label class="c-checkbox" :class="[
        getSizeClass,{
            'is-checked':isChecked,
            'is-disabled':disabled,
            'is-bordered':border,
        }]">
        <span 
            class="c-checkbox__input" 
            :class="{
                'is-checked':isChecked,
                'is-disabled':disabled,
            }"
        >
            <span class="c-checkbox__inner"></span>
            <input 
                class="c-checkbox__original"
                type="checkbox"
                :disabled="disabled"
                v-model="model"
                :value="label"
                v-bind:checked="isChecked"
                v-on:change="$emit('change',label)"
            />
        </span>
        
        <span v-if="$slots.default" class="c-checkbox__label">
            <slot></slot>            
        </span>
        <span v-if="!$slots.default" class="c-checkbox__label">
            {{this.label}}
        </span>
    </label>
</template>

<script>
import Emitter from './../mixins/emitter'

export default {
    name: 'CCheckbox',
    mixins:[Emitter],
    props:{
        value:{},
        checkValue: [String,Number,Boolean],
        label: [String,Number,Boolean],
        disabled: Boolean,
        border: Boolean,
        size:{
            type: String,
            validator: function (value) {
                return ['medium', 'small', 'mini','tiny'].indexOf(value) !== -1
            }
        }
    },
    computed:{
        _checkboxGroup(){
            let checkboxGroup=null
            let parent = this.$parent
            while(parent){
                const parentComponentName = parent.$options.componentName
                const isCheckboxGroup = "CCheckboxGroup"===parentComponentName
                if(isCheckboxGroup){
                    checkboxGroup = parent
                    break
                }else{
                    parent = parent.$parent
                }
            }
            return checkboxGroup
        },
        isGroup(){
            let parent = this.$parent
            while(parent){
                const parentComponentName = parent.$options.componentName
                const isCheckboxGroup = "CCheckboxGroup"===parentComponentName
                if(isCheckboxGroup){
                    return true
                }else{
                    parent = parent.$parent
                }
            }
            return false
        },
        model:{
            get(){
                console.log("checkbox->",this.value)
                let result;
                if(this.isGroup){
                    console.log("_checkboxGroup->",this._checkboxGroup)
                    const parentValue = this._checkboxGroup.$props.value
                    console.log(parentValue)
                    result = parentValue.indexOf(this.label) != -1
                }else{
                    result = this.value
                }
                return result
            },
            set(val){
                console.log("set val->",val)
                if(this.isGroup){
                    const parentValue = this._checkboxGroup.$props.value
                    let result = [...parentValue]
                    if(val){
                        if(result.indexOf(this.label)==-1){
                            result.push(this.label)
                        }
                    }else{
                        const findIndex = result.indexOf(this.label)
                        if(findIndex!=-1){
                            result.splice(findIndex,1)
                        }
                    }
                    this.dispatch('CCheckboxGroup', 'input', [result]);
                }else{
                    this.$emit('input', val);
                }
            }
        },
        isChecked(){
            return this.model
        },
        getSizeClass(){
            if(this.isGroup){
                if(this._checkboxGroup.$props.size){
                    return "c-checkbox--"+this._checkboxGroup.$props.size
                }else{
                    if(this.size){
                        return "c-checkbox--"+this.size
                    }
                }
            }else{
                if(this.size){
                    return "c-checkbox--"+this.size
                }
            }
            return null
        }
    }
}
</script>

<style lang="scss">
@import "./../theme/src/components/CCheckbox.scss";
</style>


