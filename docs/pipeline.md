## CocosCreator的pipeline测试

Creator的cc.assetManager的加载逻辑使用了pineline管线技术  
pipeline封装了流程处理，对于上层业务开发也有很大帮助，因此本篇文档主要是为了记录pipeline使用方法

### pipeline实现原理
* pipeline只能执行cc.AssetManager.Task
* 每个pipleline有pipes字段，这个字段记录的是**function**回调列表，pipeline做的就是依次执行这个回调列表
* pipeline类主要有两个方法**async**和**sync**，前者是同步函数，后者是异步函数（其实最终是否异步关键在于**回调**是否异步）
* **pipes**列表里的每个pipe函数里，必须要赋值task.output（除非这是最后一个pipe函数或者在pipe函数里不使用task.input）
* 如果在**pipe**函数里调用**done**时传递了参数，那么pipeline不会进行下一个pipe，将立即结束并回调complete
* 关键代码如下
``` typescript
if (result) {
    task._isFinish = true;
    task.onComplete && task.onComplete(result);
}
else {
    index++;
    if (index < self.pipes.length) {
        // move output to input
        task.input = task.output;
        task.output = null;
        self._flow(index, task);
    }
    else {
        task._isFinish = true;
        task.onComplete && task.onComplete(result, task.output);
    }
}
```
**每次进行下一个pipe前，会将task.output赋值给task.input，然后将task.output=null**

### assetManager的piplline
1. pipeline 主要负责load流程处理
2. fetchPipeline 调用cc.assetManager.preLoadxx会使用这个pipeline，其实和上面那个差不多
3. transformPipeline 主要负责url转换

#### People.ts
``` typescript
export default class People
{
    public isEat:boolean = false;
    public isSing:boolean = false;
    public isDance:boolean = false;
    public isSleep:boolean = false;
    public isDrive:boolean = false;
    public isLearn:boolean = false;

    private _name:string;
    constructor(n){
        this._name = n;
    }
}
```

#### TestPipeline
``` typescript
import People from "./People";

export default class TestPipeline
{
    private _line1:cc.AssetManager.Pipeline;
    private _line2:cc.AssetManager.Pipeline;
    constructor(){
        this._line1 = new cc.AssetManager.Pipeline("_line1",[this.eat, this.sing, this.sleep]);
        this._line2 = new cc.AssetManager.Pipeline("_line2",[this.eat, this.dance, this.drive, this.learn, this.sleep]);

        let task:cc.AssetManager.Task = cc.AssetManager.Task.create({
            onComplete:(err, result)=>{
                console.log(result);
            },
            input:new People("蜡笔小新")
        });
        this._line1.async(task);

        // task = cc.AssetManager.Task.create({
        //     onComplete:(err, result)=>{
        //         console.log(result);
        //     },
        //     input:new People("樱桃小丸子")
        // });
        // this._line2.async(task);
    }

    public eat(task:cc.AssetManager.Task, done){
        console.log(`${task.input._name}开始eat`);
        setTimeout(() => {
            task.input.isEat = true;
            task.output = task.input;
            done();
        }, 1);
    }

    public sing(task:cc.AssetManager.Task, done){
        console.log(`${task.input._name}开始sing`);
        setTimeout(()=>{
            task.input.isSing = true;
            task.output = task.input;
            done();
        }, 1);
    }

    public dance(task:cc.AssetManager.Task, done){
        console.log(`${task.input._name}开始dance`);
        setTimeout(()=>{
            task.input.isDance = true;
            task.output = task.input;
            done();
        }, 1);
    }

    public sleep(task:cc.AssetManager.Task, done){
        console.log(`${task.input._name}开始sleep`);
        setTimeout(()=>{
            task.input.isSleep = true;
            task.output = task.input;
            done();
        }, 1);
    }

    public drive(task:cc.AssetManager.Task, done){
        console.log(`${task.input._name}开始drive`);
        setTimeout(()=>{
            task.input.isDrive = true;
            task.output = task.input;
            done();
        }, 1);
    }

    public learn(task:cc.AssetManager.Task, done){
        console.log(`${task.input._name}开始learn`);
        setTimeout(()=>{
            task.input.isLearn = true;
            task.output = task.input;
            done();
        }, 1);
    }
}
```