# Cocos-RenderEngine部分学习

> Author: huzi(moustache)<br>Date: 18-10-26 17:17

## RenderFlow

- 所处文件：render-flow.js

- 作用：渲染时根据node._renderFlag依次更新需要渲染的值。和render-walker混合使用。

- 关键部分：

  - 构造：

    ~~~javascript
    function RenderFlow () {
        this._func = init;
        this._next = null;
    }
    
    function init (node) {
        let flag = node._renderFlag;
        let r = flows[flag] = getFlow(flag);
        r._func(node);
    }
    
    // ......
    
    let flows = {};
    
    RenderFlow.init = function (walker) {
        _walker = walker;
    
        flows[0] = EMPTY_FLOW;
    
        for (let i = 1; i < FINAL; i++) {
            flows[i] = new RenderFlow();
        }
    };
    ~~~

    构造了1024(2^10)个空的Render，每个空的Render中的_func是上图的init函数，实际上类似于c++中的动态链接，即**运行时执行init函数生成真正的Render.\_func**。

  - getFlow(flag)：

    ~~~javascript
    const DONOTHING = 0;
    const LOCAL_TRANSFORM = 1 << 0;
    const WORLD_TRANSFORM = 1 << 1;
    const TRANSFORM = LOCAL_TRANSFORM | WORLD_TRANSFORM;
    const UPDATE_RENDER_DATA = 1 << 2;
    const OPACITY = 1 << 3;
    const COLOR = 1 << 4;
    const RENDER = 1 << 5;
    const CUSTOM_IA_RENDER = 1 << 6;
    const CHILDREN = 1 << 7;
    const POST_UPDATE_RENDER_DATA = 1 << 8;
    const POST_RENDER = 1 << 9;
    const FINAL = 1 << 10;
    
    // ......
    
    function createFlow (flag, next) {
        let flow = new RenderFlow();
        flow._next = next || EMPTY_FLOW;
    
        switch (flag) {
                // 其中，flow._doNoting都是事先定义好的函数
            case DONOTHING: 
                flow._func = flow._doNothing;
                break;
            case LOCAL_TRANSFORM: 
                flow._func = flow._localTransform;
                break;
            case WORLD_TRANSFORM: 
                flow._func = flow._worldTransform;
                break;
            case COLOR:
                flow._func = flow._color;
                break;
            case OPACITY:
                flow._func = flow._opacity;
                break;
            case UPDATE_RENDER_DATA:
                flow._func = flow._updateRenderData;
                break;
            case RENDER: 
                flow._func = flow._render;
                break;
            case CUSTOM_IA_RENDER:
                flow._func = flow._customIARender;
                break;
            case CHILDREN: 
                flow._func = flow._children;
                break;
            case POST_UPDATE_RENDER_DATA: 
                flow._func = flow._postUpdateRenderData;
                break;
            case POST_RENDER: 
                flow._func = flow._postRender;
                break;
        }
    
        return flow;
    }
    
    // ......
    
    function getFlow (flag) {
        let flow = null;
        let tFlag = FINAL;
        while (tFlag > 0) {
            if (tFlag & flag)
                flow = createFlow(tFlag, flow);
            tFlag = tFlag >> 1;
        }
        return flow;
    }
    ~~~

    getFlow函数中，实际上就是生成一个链表。

    链表中，根据flag每一位的二进制表示（在createFlow中），绑定一个函数。

  - 部分绑定函数和遍历函数的具体实现：

    ~~~javascript
    _proto._worldTransform = function (node) {
        _walker.worldMatDirty ++;
    
        let t = node._matrix;
        let position = node._position;
        t.m12 = position.x;
        t.m13 = position.y;
    
        node._mulMat(node._worldMatrix, node._parent._worldMatrix, t);
        node._renderFlag &= ~WORLD_TRANSFORM;
        this._next._func(node);
    
        _walker.worldMatDirty --;
    };
    
    // ......
    
    _proto._children = function (node) {
        let cullingMask = _cullingMask;
    
        let parentOpacity = _walker.parentOpacity;
        _walker.parentOpacity *= (node._opacity / 255);
    
        let worldTransformFlag = _walker.worldMatDirty ? WORLD_TRANSFORM : 0;
        let worldOpacityFlag = _walker.parentOpacityDirty ? COLOR : 0;
    
        let children = node._children;
        for (let i = 0, l = children.length; i < l; i++) {
            let c = children[i];
            if (!c._activeInHierarchy || c._opacity === 0) continue;
            _cullingMask = c._cullingMask = c.groupIndex === 0 ? cullingMask : 1 << c.groupIndex;
            c._renderFlag |= worldTransformFlag | worldOpacityFlag;
    
            // TODO: Maybe has better way to implement cascade opacity
            c._color.a = c._opacity * _walker.parentOpacity;
            flows[c._renderFlag]._func(c);
            c._color.a = 255;
        }
    
        _walker.parentOpacity = parentOpacity;
    
        this._next._func(node);
    
        _cullingMask = cullingMask;
    };
    ~~~

    在这个函数中，通过使用在函数内部使用_walker.worldMatDirty ++，保证子节点都能进行更新。

