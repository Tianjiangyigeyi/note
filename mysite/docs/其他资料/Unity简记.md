# Unity简记

## Unity 生命期

- AWake：有且仅有一次
- OnEnable：enabled变为true可以多次
- Start：active&&enabled有且仅有一次
- Update：每帧更新
- LateUpdate：在所有脚本都update之后才调用
- OnDisable：enabled变为false时候会调用
- OnDestroy：被销毁时会调用、即!active也会调用（前提是调用过Awake）