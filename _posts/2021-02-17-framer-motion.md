# framer-motion 빠르게 살펴보기

# 1. 어떤 일을 할 수 있나요?

framer-motion은 리액트를 위한 웹 애니메이션, 제스처 오픈소스 라이브러리입니다.

아래와 같은 기능을 제공하고 있습니다.

- 다양한 애니메이션
- 단순한 키프레임 문법 제공
- 제스쳐 (drag/tap/hover)
- 레이아웃 애니메이션
- SVG paths
- Exit 애니메이션
- 서버사이드 렌더링
- 컴포넌트 간의 애니메이션 오케스트레이팅
- CSS 변수
- ..그외 훨씬 더 많은 것들

## 1.1. 리액트 애니메이션 라이브러리 비교

- https://openbase.com/categories/js/best-react-animation-libraries?orderBy=RECOMMENDED&

![chakra-framer](/assets/images/posts/chakra-framer.png)

# 2. 시작하기

```bash
yarn add framer-motion
```

```jsx
import { motion } from "framer-motion";

export const MyComponent = () => <motion.div animate={{ rotate: 360 }} transition={{ duration: 2 }} />;
```

# 3. API 돌아보기

## 3.1. animation

간단하게 애니메이션 설정값을 바로 사용할 수 있습니다.

```jsx
<motion.div animate={{ x: 0 }} />
```

## 3.2. initial

필요한 경우에 애니메이션 초기값을 지정합니다.

```jsx
<motion.div initial={{ x: "-100vw" }} animate={{ x: 0 }} />
```

## 3.3. transition

Motion은 애니메이션을 적용할 속성에 따라 적절한 트랜지션 기본값을 미리 제공합니다. 예를 들어, x 또는 scale과 같은 프로퍼티는 spring을 사용합니다. opacity나 color와 같은 프로퍼티는 tween을 사용합니다.

```jsx
<motion.div animate={{ x: 100 }} transition={{ ease: "easeOut", duration: 2 }} />
```

### 3.3.1 Spring

[https://csb-b3yhc.netlify.app/](https://csb-b3yhc.netlify.app/)

```jsx
<motion.div animate={{ scale: 1.5 }} transition={{ type: "spring", damping: 3 }} />
```

### 3.3.2 Repeat and delay

[https://csb-gp4b9.netlify.app/](https://csb-gp4b9.netlify.app/)

```jsx
<motion.div animate={{ rotate: 360 }} transition={{ ease: "linear", duration: 2, repeat: Infinity, delay: 1 }} />
```

### 3.3.3 Delay between repetitions

[https://csb-7zw1d.netlify.app/](https://csb-7zw1d.netlify.app/)

```jsx
<motion.div
  animate={{ rotate: 360 }}
  transition={{
    duration: 2,
    repeat: Infinity,
    delay: 1,
    repeatDelay: 1,
  }}
/>
```

### 3.3.4 Repeat type and Bézier curves

[https://csb-tz3ht.netlify.app/](https://csb-tz3ht.netlify.app/)

```jsx
<motion.div
  y={-90}
  animate={{ y: 70, rotate: 360 }}
  transition={{
    delay: 1,
    duration: 2,
    ease: [0.075, 0.82, 0.165, 1],
    repeat: Infinity,
    repeatType: "reverse", // loop(default), mirror(Earlier, flip), reverse(Earlier, yoyo)
  }}
/>
```

### 3.3.5 Repeating spring animations

[https://csb-vedl9.netlify.app/](https://csb-vedl9.netlify.app/)

```jsx
<motion.div
  initial={{ x: -200 }}
  animate={{ x: 200 }}
  transition={{
    type: "spring",
    repeat: Infinity,
    repeatType: "mirror",
    repeatDelay: 0.1,
  }}
/>
```

## 3.4. Gestures

### 3.4.1. hover

`hover` 제스처는 `onMouseEnter`과 `onMouseLeave`와 다릅니다. `hover`는 실제 마우스 기기 동작에 의해서만 발생하는 것을 보장합니다. (브라우저의 마우스 이벤트가 터치 입력에서도 마우스 동작으로 간주되는 것과 상반됩니다.)

```jsx
<motion.a
  whileHover={{ scale: 1.2 }} // Animation helper
  onHoverStart={(event, info) => {}}
  onHoverEnd={(event, info) => {}}
/>
```

### 3.4.2. tap

`tap` 제스처는 포인터가 동일한 컴포넌트를 눌렀다가 놓았을 때 감지합니다.

`tap`이 성공적으로 완료되면 `tap` 이벤트가 발생하고 `tap`이 컴포넌트 외부에서 종료되면 `tapCancel` 이벤트가 발생합니다.

만약에 `tap`이 가능한 컴포넌트가 draggable 컴포넌트의 자식이라면, 3px 이상 포인터가 이동할 때 `tap` 제스처가 자동으로 취소됩니다.

```jsx
function onTap(event, info) {
  console.log(info.point.x, info.point.y);
}

<motion.a
  whileTap={{ scale: 0.8 }} // Animation helper
  onTap={onTap}
  onTapStart={(event, info) => {}}
  onTapCancel={(event, info) => {}}
/>;
```

### 3.4.3. pan

`pan` 제스처는 포인터가 구성 요소를 누르고 3픽셀 이상 이동할 때 인식합니다. `pan` 제스처는 포인터가 해제되면 종료됩니다.

```jsx
function onPan(event, info) {
  console.log(info.point.x, info.point.y);
}

<motion.a onPan={onPan} onPanStart={(event, info) => {}} onPanEnd={(event, info) => {}} />;
```

### 3.4.3. Drag

`drag` 제스처는 `pan` 제스처의 규칙을 따르지만 컴포넌트의 x/y 축에 포인터 이동을 적용합니다.

```jsx
<motion.div drag />

// draggable 컴포넌트가 layout 애니메이션에도 사용되는 경우
// drag 제스처가 x/y 변형 대신 컴포넌트 뷰포트 박스에 적용됩니다.
<motion.div drag layout />

// drag: 이동한 축을 설정합니다.
<motion.div drag="x" /> //false(default), ture(both), x, y

// dragConstraints: 이동 가능한 픽셀을 제한합니다.
<motion.div
  drag="x"
  dragConstraints={{ left: 0, right: 300 }}
/>

// dragConstraints: 다른 컴포넌트 ref로 제한할 수 있습니다.
const MyComponent = () => {
  const constraintsRef = useRef(null)

  return (
     <motion.div ref={constraintsRef}>
         <motion.div drag dragConstraints={constraintsRef} />
     </motion.div>
  )
}

// dragElastic: 제한범위 바운더리에서의 움직임 허용치를 나타냅니다.
<motion.div
  drag
  dragConstraints={{ left: 0, right: 300 }}
  dragElastic={0.2} // 0.5(default), 0(no movement), 1(full movement)
/>

// dragMomentum: 드래그가 끝났을 때 pan 제스처 탄력을 적용합니다.
<motion.div
  drag
  dragConstraints={{ left: 0, right: 300 }}
  dragMomentum={false} // treu(default), false
/>

// dragTransition: 드래그 관성을 변경할 수 있습니다.
<motion.div
  drag
  dragTransition={{ bounceStiffness: 600, bounceDamping: 10 }}
/>

// dragPropagation: drag 버블링 허용여부를 설정합니다.
<motion.div drag="x" dragPropagation />

// dragControls & useDragControls
// 일반적으로 누르는 동작으로 컴포넌트가 드래그가 시작되고 움직입니다.
// dragContrls는 다른 컴포넌트를 통해 드래그를 컨트롤할 때 유용합니다.
const dragControls = useDragControls()

function startDrag(event) {
  dragControls.start(event, { snapToCursor: true })
}

return (
  <>
    <div onPointerDown={startDrag} />
    <motion.div drag="x" dragControls={dragControls} />
  </>
)

// onDrag, onDragStart, onDragEnd
<motion.div
  drag
  onDrag={
    (event, info) => console.log(info.point.x, info.point.y)
  }
  onDragStart={
    (event, info) => console.log(info.point.x, info.point.y)
  }
  onDragEnd={
    (event, info) => console.log(info.point.x, info.point.y)
  }
/>

// dragDirectionLock
<motion.div
  drag
  dragDirectionLock
  onDirectionLock={axis => console.log(axis)}
/>
```

# 4. 컴포넌트

## 4.1. motion

`motion` 컴포넌트는 60fps 애니메이션과 제스처에 최적화된 DOM 기본요소입니다. 모든 HTML, SVG 엘리먼트에 `motion.`을 붙이면 motion 컴포넌트가 됩니다. (e.g. `motion.div,` `motion.circle` )

아무런 props도 전달하지 않으면 기본 엘리먼트처럼 동작하지만, props 전달을 통해 아래와 같은 일을 할 수 있습니다.

- 컴포넌트 애니메이션
- drag, pan, hover, tap 제스처 추가
- 제스처에 대한 애니메이션 응답
- 리액트 트리에 대해 variants를 통한 깊은 애니메이션 제공

## 4.2. AnimateSharedLayout

`AnimateSharedLayout` 컴포넌트를 사용하여 레이아웃 애니메이션을 수행할 수 있습니다.

### 4.2.1. 상태를 공유하지 않는 컴포넌트 그룹 애니메이션

[https://codesandbox.io/embed/framer-motion-2-animating-shared-layouts-1cpd0?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/framer-motion-2-animating-shared-layouts-1cpd0?fontsize=14&hidenavigation=1&theme=dark)

```jsx
import { AnimateSharedLayout } from "framer-motion";

function Item({ content }) {
  const [isOpen, setIsOpen] = useState(false);

  return <motion.div layout>{isOpen && content}</motion.div>;
}

function List({ items }) {
  /**
   * This wrapping motion.ul, and sibling
   * Item components won't animate layout
   * when an Item opens/closes
   */
  return (
    <motion.ul layout>
      {items.map((item) => (
        <Item content={item.content} />
      ))}
    </motion.ul>
  );
}

function List({ items, selectedId }) {
  return (
    <AnimateSharedLayout>
      <motion.ul layout>
        {items.map((item) => (
          <Item content={item.content} />
        ))}
      </motion.ul>
    </AnimateSharedLayout>
  );
}
```

### 4.2.2 layoutId를 공유하는 서로 다른 컴포넌트 애니메이션

[https://codesandbox.io/embed/framer-motion-2-animatesharedlayout-animate-between-different-components-dy0bv?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/framer-motion-2-animatesharedlayout-animate-between-different-components-dy0bv?fontsize=14&hidenavigation=1&theme=dark)

```jsx
import { motion, AnimateSharedLayout } from "framer-motion";
import "./styles.css";

export default function App() {
  const [selected, setSelected] = useState(colors[0]);

  return (
    <AnimateSharedLayout>
      <ul>
        {colors.map((color) => (
          <Item key={color} color={color} isSelected={selected === color} onClick={() => setSelected(color)} />
        ))}
      </ul>
    </AnimateSharedLayout>
  );
}

function Item({ color, isSelected, onClick }) {
  return (
    <li className="item" onClick={onClick} style={{ backgroundColor: color }}>
      {isSelected && <motion.div layoutId="outline" className="outline" initial={false} animate={{ borderColor: color }} transition={spring} />}
    </li>
  );
}

const colors = ["#ff0055", "#0099ff", "#22cc88", "#ffaa00"];

const spring = {
  type: "spring",
  stiffness: 500,
  damping: 30,
};
```

## 4.3. AnimatePresence

`AnimatePresence` 컴포넌트는 React 트리에서 컴포넌트가 제거될 때의 애니메이션을 처리합니다. 컴포넌트가 언마운트될 때를 알리고, 애니메이션이 끝날때까지 컴포넌트의 언마운트 시점을 미룰 수 있습니다.

```jsx
import { motion, AnimatePresence } from "framer-motion";

export const MyComponent = ({ isVisible }) => <AnimatePresence>{isVisible && <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} exit={{ opacity: 0 }} />}</AnimatePresence>;
```

# 5. framer-motion with Chakra-ui

```jsx
import React from "react";
import { forwardRef, ChakraProps, chakra } from "@chakra-ui/react";
import { motion, MotionProps, isValidMotionProp } from "framer-motion";

// 1. Create a custom motion component from Box
const MotionBox = motion.custom(
  (forwardRef < MotionProps) & ChakraProps,
  "div" >
    ((props, ref) => {
      const chakraProps = Object.fromEntries(Object.entries(props).filter(([key]) => !isValidMotionProp(key)));

      return <chakra.div ref={ref} {...chakraProps} />;
    })
);

// 2. You'll get access to `motion` and `chakra` props in `MotionBox`
function Example() {
  return <MotionBox size="40px" bg="red.300" drag="x" dragConstraints={{ left: -100, right: 100 }} whileHover={{ scale: 1.1 }} whileTap={{ scale: 0.9 }} />;
}
```

# Reference

- 공식홈페이지: [https://www.framer.com/motion/](https://www.framer.com/motion/)
- API: [https://www.framer.com/api/motion](https://www.framer.com/api/motion)
- 프레이머북: [https://framerbook.com/x/framer-motion/](https://framerbook.com/x/framer-motion/)
- The Net Ninja Framer Motion Tutorial Youtube: [https://www.youtube.com/watch?v=2V1WK-3HQNk&list=PL4cUxeGkcC9iHDnQfTHEVVceOEBsOf07i](https://www.youtube.com/watch?v=2V1WK-3HQNk&list=PL4cUxeGkcC9iHDnQfTHEVVceOEBsOf07i)
- The Net Ninja Framer Motion Tutorial github: [https://github.com/iamshaunjp/framer-motion](https://github.com/iamshaunjp/framer-motion)
- Animating Routes With Reach/Router: [https://codesandbox.io/embed/react-pose-route-transitions-with-reach-router-76mf0](https://codesandbox.io/embed/react-pose-route-transitions-with-reach-router-76mf0)
