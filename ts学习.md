# cocos creator四种事件

鼠标事件类型
cc.[Node](https://so.csdn.net/so/search?q=Node&spm=1001.2101.3001.7020).EventType.MOUSE_DOWN:当鼠标在目标节点区域按下时触发一次 事件名：‘mousedown’
cc.Node.EventType.MOUSE_ENTER:当鼠标移入目标节点区域时，不论是否按下 事件名：‘mouseenter’
cc.Node.EventType.MOUSE_MOVE:当鼠标在目标节点在目标节点区域中移动时，不论是否按下 事件名：‘mousemove’
cc.Node.EventType.MOUSE_LEAVE:当鼠标移动出目标节点区域是，不论是否按下 事件名：’mouseleave’
cc.Node.EventType.MOUSE_UP:当鼠标从按下状态松开时触发一次 事件名：‘mouseup’
cc.Node.EventType.MOUSE_WHEEL:当鼠标滚轮滚动时 事件名：‘mousewheel’

触摸事件
触摸事件在移动平台和桌面平台都会触发。
cc.Node.EventType.TOUCH_START:当手指触点露在目标节点区域时 事件名：‘touchstart’
cc.Node.EventType.TOUCH_MOVE:当手指在屏幕上目标点区域内移动时 事件名:’touchmove’
cc.Node.EventType.TOUCH_END:当手指在目标节点区域内离开屏幕时 事件名：‘touchend’
cc.Node.EventType.TOUCH_CANCEL:当手指在目标节点区域外离开屏幕时 事件名：’touchcancel’

全局系统事件
键盘事件和重力传感事件，是通过cc.systemEvent.on来绑定事件。
cc.SystemEvent.EventType.KEY_DOWN(键盘按下)
cc.SystemEvent.EventType.KEY_UP(键盘释放)
cc.SystemEvent.EventType.DEVICEMOTION(设备重力传感)

cc.systemEvent.on(cc.SystemEvent.EventType.KEY_DOWN,function(event){
},this);
cc.systemEvent.on(cc.SystemEvent.EventType.KEY_UP,function（event){
}，this）；

***

鼠标滚轮事件（移动视角）

```typescript
import { Vec3,systemEvent, SystemEvent, EventMouse } from 'cc';
//成员变量
const v3_1 = new Vec3();
//局部变量
private _position = new Vec3();
private _euler = new Vec3();
@property
public moveSpeed = 1;

public onLoad () {
    systemEvent.on(SystemEvent.EventType.MOUSE_WHEEL, this.onMouseWheel, this); 
    Vec3.copy(this._euler, this.node.eulerAngles);
    Vec3.copy(this._position, this.node.position);
}
public onMouseWheel (e: EventMouse) {
    // delta is positive when scroll down  向下滚动时delta为正
    const delta = -e.getScrollY() * this.moveSpeed * 0.1; 
    Vec3.transformQuat(v3_1, Vec3.UNIT_Z, this.node.rotation);
    Vec3.scaleAndAdd(this._position, this.node.position, v3_1, delta);
}
```

---

当按下按键时事件（移动视角）

```typescript
import {Vec3,systemEvent, SystemEvent} from 'cc'
//成员变量
const KEYCODE = {		
    W: 'W'.charCodeAt(0),		//视角向里（Z-）
    S: 'S'.charCodeAt(0),		//视角向里（Z+）
    A: 'A'.charCodeAt(0),
    D: 'D'.charCodeAt(0),		
    Q: 'Q'.charCodeAt(0),		//视角向下（Y-）
    E: 'E'.charCodeAt(0),		//视角向上（Y+）
    SHIFT: macro.KEY.shift,		//加速
};
//局部变量
private _speedScale = 1;	
private _position = new Vec3();
private _euler = new Vec3();
public onLoad(){
    systemEvent.on(SystemEvent.EventType.KEY_DOWN, this.onKeyDown, this);
    
    systemEvent.off(SystemEvent.EventType.KEY_UP, this.onKeyUp, this);
    Vec3.copy(this._euler, this.node.eulerAngles);
    Vec3.copy(this._position, this.node.position);
}
public onKeyDown (e: EventKeyboard) {
    const v = this._velocity;
    if      (e.keyCode === KEYCODE.SHIFT) { 
        this._speedScale = this.moveSpeedShiftScale; 
    }
    else if (e.keyCode === KEYCODE.W) { if (v.z === 0) { v.z = -1; } }
    else if (e.keyCode === KEYCODE.S) { if (v.z === 0) { v.z =  1; } }
    else if (e.keyCode === KEYCODE.A) { if (v.x === 0) { v.x = -1; } }
    else if (e.keyCode === KEYCODE.D) { if (v.x === 0) { v.x =  1; } }
    else if (e.keyCode === KEYCODE.Q) { if (v.y === 0) { v.y = -1; } }
    else if (e.keyCode === KEYCODE.E) { if (v.y === 0) { v.y =  1; } }
}
public onKeyUp (e: EventKeyboard) {
    const v = this._velocity;
    if      (e.keyCode === KEYCODE.SHIFT) { this._speedScale = 1; }
    else if (e.keyCode === KEYCODE.W) { if (v.z < 0) { v.z = 0; } }
    else if (e.keyCode === KEYCODE.S) { if (v.z > 0) { v.z = 0; } }
    else if (e.keyCode === KEYCODE.A) { if (v.x < 0) { v.x = 0; } }
    else if (e.keyCode === KEYCODE.D) { if (v.x > 0) { v.x = 0; } }
    else if (e.keyCode === KEYCODE.Q) { if (v.y < 0) { v.y = 0; } }
    else if (e.keyCode === KEYCODE.E) { if (v.y > 0) { v.y = 0; } }
}
```

---

触摸事件（手机移动视角）

```typescript
import {systemEvent,SystemEvent,Touch,game} from 'cc'
//成员变量
const v2_1 = new Vec2();
const v2_2 = new Vec2();
//局部变量
private _velocity = new Vec3();
private _euler = new Vec3();
private _position = new Vec3();
public onload (){
    systemEvent.on(SystemEvent.EventType.TOUCH_START, this.onTouchStart, this);
    systemEvent.on(SystemEvent.EventType.TOUCH_MOVE, this.onTouchMove, this);
    systemEvent.on(SystemEvent.EventType.TOUCH_END, this.onTouchEnd, this);
    Vec3.copy(this._euler, this.node.eulerAngles);
    Vec3.copy(this._position, this.node.position);
}
public onTouchStart (e: Touch) {
    if (game.canvas.requestPointerLock) { game.canvas.requestPointerLock(); }
}
public onTouchMove (e: Touch) {
    e.getStartLocation(v2_1);
    if (v2_1.x > game.canvas.width * 0.4) { // rotation
        e.getDelta(v2_2);
        this._euler.y -= v2_2.x * this.rotateSpeed * 0.1;
        this._euler.x += v2_2.y * this.rotateSpeed * 0.1;
    } else { // position
        e.getLocation(v2_2);
        Vec2.subtract(v2_2, v2_2, v2_1);
        this._velocity.x = v2_2.x * 0.01;
        this._velocity.z = -v2_2.y * 0.01;
    }
}
public onTouchEnd (e: Touch) {
    if (document.exitPointerLock) { document.exitPointerLock(); }
    e.getStartLocation(v2_1);
    if (v2_1.x < game.canvas.width * 0.4) { // position
        this._velocity.x = 0;
        this._velocity.z = 0;
    }
}
```

---

各类视角移动结束时事件

```typescript
/**
systemEvent.off
删除之前用同类型，回调，目标或 useCapture 注册的事件监听器.
如果只传递 type，将会删除 type 类型的所有事件监听器。
*/
public onDestroy () {
    systemEvent.off(SystemEvent.EventType.MOUSE_WHEEL, this.onMouseWheel, this);
    systemEvent.off(SystemEvent.EventType.KEY_DOWN, this.onKeyDown, this);
    systemEvent.off(SystemEvent.EventType.KEY_UP, this.onKeyUp, this);
    systemEvent.off(SystemEvent.EventType.TOUCH_START, this.onTouchStart, this);
    systemEvent.off(SystemEvent.EventType.TOUCH_MOVE, this.onTouchMove, this);
    systemEvent.off(SystemEvent.EventType.TOUCH_END, this.onTouchEnd, this);
}
public update (dt: number) {
    // position
    Vec3.transformQuat(v3_1, this._velocity, this.node.rotation);
    Vec3.scaleAndAdd(this._position, this._position, v3_1, this.moveSpeed * this._speedScale);
    Vec3.lerp(v3_1, this.node.position, this._position, dt / this.damp);
    this.node.setPosition(v3_1);
    // rotation
    Quat.fromEuler(qt_1, this._euler.x, this._euler.y, this._euler.z);
    Quat.slerp(qt_1, this.node.rotation, qt_1, dt / this.damp);
    this.node.setRotation(qt_1);
}
```

