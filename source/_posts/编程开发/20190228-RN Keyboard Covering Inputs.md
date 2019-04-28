---
title: React Native Keyboard Covering Inputs
tags:
  - JavaScript
  - React Native
comments: true
date: 2019-02-28 10:10:45
categories: 编程开发
---
当键盘遮挡住被输入TextInput时，尝试过几种解决思路：

1. 外侧包一层`ScrollView`，然后通过`ScrollView`的`scrollTo()`来滚到键盘上方，其中的难点在于 **怎么计算需要滚动的值**，原lego里的Scroll + Input有对应实现，但有bug💔
2. 用`react native`的`KeyboardAvoidingView`组件，但在需要用`ScrollView`的地方很诡异，遂放弃
3. 用`transform`向Y轴平移啊，还不用动`TextInput`，结合 `animation`提升用户体验，简直爽得起飞🛫️，😄
<!--more-->

如下：

```javascript
export default class KeyboardShift extends Component {
  state = {
    shift: new Animated.Value(0),
  };

  componentWillMount() {
    this.keyboardDidShowSub = Keyboard.addListener('keyboardDidShow', this.handleKeyboardDidShow);
    this.keyboardDidHideSub = Keyboard.addListener('keyboardDidHide', this.handleKeyboardDidHide);
  }

  componentWillUnmount() {
    this.keyboardDidShowSub.remove();
    this.keyboardDidHideSub.remove();
  }

  handleKeyboardDidShow = (event) => {
    const { height: windowHeight } = Dimensions.get('window');
    const keyboardHeight = event.endCoordinates.height;
    const currentlyFocusedField = TextInputState.currentlyFocusedField();
    UIManager.measure(currentlyFocusedField, (originX, originY, width, height, pageX, pageY) => {
      const fieldHeight = height;
      const fieldTop = pageY;
      const gap = (windowHeight - keyboardHeight) - (fieldTop + fieldHeight) - offset;
      if (gap >= 0) {
        return;
      }
      Animated.timing(
        this.state.shift,
        {
          toValue: gap,
          duration: 300,
        }
      ).start();
    });
  }

  handleKeyboardDidHide = () => {
    Animated.timing(
      this.state.shift,
      {
        toValue: 0,
        duration: 300,
      }
    ).start();
  }

  render() {
    const { children, ...other } = this.props;
    const { shift } = this.state;
    return (
      <ScrollView
        keyboardShouldPersistTaps="handled"
        {...other}
      >
        <Animated.View style={[styles.container, { transform: [{ translateY: shift }] }]}>
          {children}
        </Animated.View>
      </ScrollView>
    );
  }
}

KeyboardShift.propTypes = {
  children: PropTypes.element.isRequired,
};


```
# ref

- [React Native Keyboard Covering Inputs – codeburst](https://codeburst.io/react-native-keyboard-covering-inputs-72a9d3072689)