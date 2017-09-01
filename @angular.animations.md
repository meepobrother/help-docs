
# @angular/animations

```ts
export { AnimationBuilder, AnimationFactory } from './animation_builder';
export { AnimationEvent } from './animation_event';
export { 
    AUTO_STYLE, 
    AnimateChildOptions, 
    AnimateTimings, 
    AnimationAnimateChildMetadata, 
    AnimationAnimateMetadata, 
    AnimationAnimateRefMetadata, 
    AnimationGroupMetadata, 
    AnimationKeyframesSequenceMetadata, 
    AnimationMetadata, 
    AnimationMetadataType, 
    AnimationOptions, 
    AnimationQueryMetadata, 
    AnimationQueryOptions, 
    AnimationReferenceMetadata, 
    AnimationSequenceMetadata, 
    AnimationStaggerMetadata, 
    AnimationStateMetadata, 
    AnimationStyleMetadata, 
    AnimationTransitionMetadata, 
    AnimationTriggerMetadata, 
    animate, 
    animateChild, 
    animation, 
    group, 
    keyframes, 
    query, 
    sequence, 
    stagger, 
    state, 
    style, 
    transition, 
    trigger, 
    useAnimation, 
    ɵStyleData 
} from './animation_metadata';
export { AnimationPlayer, NoopAnimationPlayer } from './players/animation_player';
// export * from './private_export';
export {AnimationGroupPlayer as ɵAnimationGroupPlayer} from './players/animation_group_player';
export const ɵPRE_STYLE = '!';
```

### AnimationBuilder

```ts
export declare abstract class AnimationBuilder {
    abstract build(animation: AnimationMetadata | AnimationMetadata[]): AnimationFactory;
}
```


### AnimationFactory

```ts
export declare abstract class AnimationFactory {
    abstract create(element: any, options?: AnimationOptions): AnimationPlayer;
}
```

### AnimationPlayer

```ts

export interface AnimationPlayer {
    onDone(fn: () => void): void;
    onStart(fn: () => void): void;
    onDestroy(fn: () => void): void;
    init(): void;
    hasStarted(): boolean;
    play(): void;
    pause(): void;
    restart(): void;
    finish(): void;
    destroy(): void;
    reset(): void;
    setPosition(p: any): void;
    getPosition(): number;
    parentPlayer: AnimationPlayer | null;
    readonly totalTime: number;
    beforeDestroy?: () => any;
}

```

### NoopAnimationPlayer

```ts

export declare class NoopAnimationPlayer implements AnimationPlayer {
    private _onDoneFns;
    private _onStartFns;
    private _onDestroyFns;
    private _started;
    private _destroyed;
    private _finished;
    parentPlayer: AnimationPlayer | null;
    totalTime: number;
    constructor();
    private _onFinish();
    onStart(fn: () => void): void;
    onDone(fn: () => void): void;
    onDestroy(fn: () => void): void;
    hasStarted(): boolean;
    init(): void;
    play(): void;
    private _onStart();
    pause(): void;
    restart(): void;
    finish(): void;
    destroy(): void;
    reset(): void;
    setPosition(p: number): void;
    getPosition(): number;
}

```

### AnimationGroupPlayer



```ts

import { AnimationPlayer } from './animation_player';
export declare class AnimationGroupPlayer implements AnimationPlayer {
    private _players;
    private _onDoneFns;
    private _onStartFns;
    private _finished;
    private _started;
    private _destroyed;
    private _onDestroyFns;
    parentPlayer: AnimationPlayer | null;
    totalTime: number;
    constructor(_players: AnimationPlayer[]);
    private _onFinish();
    init(): void;
    onStart(fn: () => void): void;
    private _onStart();
    onDone(fn: () => void): void;
    onDestroy(fn: () => void): void;
    hasStarted(): boolean;
    play(): void;
    pause(): void;
    restart(): void;
    finish(): void;
    destroy(): void;
    private _onDestroy();
    reset(): void;
    setPosition(p: number): void;
    getPosition(): number;
    readonly players: AnimationPlayer[];
    beforeDestroy(): void;
}

```


### AnimationEvent
```ts
export interface AnimationEvent {
    fromState: string;
    toState: string;
    totalTime: number;
    phaseName: string;
    element: any;
    triggerName: string;
}
```

### AnimateTimings

```ts
export declare type AnimateTimings = {
    duration: number;
    delay: number;
    easing: string | null;
};
```

### AnimationOptions

```ts
export interface AnimationOptions {
    delay?: number | string;
    params?: {
        [name: string]: any;
    };
}
```

### AnimateChildOptions
```ts
export interface AnimateChildOptions extends AnimationOptions {
    duration?: number | string;
}
```

### AnimationMetadataType

```ts

export declare const enum AnimationMetadataType {
    State = 0,
    Transition = 1,
    Sequence = 2,
    Group = 3,
    Animate = 4,
    Keyframes = 5,
    Style = 6,
    Trigger = 7,
    Reference = 8,
    AnimateChild = 9,
    AnimateRef = 10,
    Query = 11,
    Stagger = 12,
}

```