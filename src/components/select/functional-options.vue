
<script>
    const returnArrayFn = () => [];

    export default {
        props: {
            options: {
                type: Array,
                default: returnArrayFn
            },
            slotOptions: {
                type: Array,
                default: returnArrayFn
            },
            slotUpdateHook: {
                type: Function,
                default: () => {}
            },
        },
        functional: true,
        render(h, {props, parent}) {
            // to detect changes in the $slot children/options we do this hack
            // so we can trigger the parents computed properties and have everything reactive
            // although $slot.default is nodeText
            // if (props.slotOptions !== parent.$slots.default) props.slotUpdateHook();
            // 之前采用VNode的判断方法可能存在一些问题：在数据没变化的时候，props.slotOptions !== parent.$slots.default也存在为true的可能。
            // 为了在数据变化后，可以清除query值，而不影响其他的正常使用，所以采用下面这种检测Key值变化的hack方法。
            // 数据变化后，不清除query，会导致validateOption方法判断不准确。
            if(props.slotOptions.length > 0) {
                for(let i in props.slotOptions) {
                    if(props.slotOptions[i].key !== parent.$slots.default[i].key) {
                        props.slotUpdateHook();
                        break;
                    }
                }
            }
            if(props.slotOptions && parent.$slots.default && props.slotOptions.length !== parent.$slots.default.length) props.slotUpdateHook()

            return props.options;
        }
    };
</script>
