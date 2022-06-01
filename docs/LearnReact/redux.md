<!--
 * @Author: Mia
 * @Date: 2022-05-27 15:14:18
 * @Description: 
-->

## Store
store 将 actions 和 reducers 结合起来
- 保存应用的state
- 通过`getState()`拿到state
- 通过`dispatch(action)`更新state
- 通过`subscribe(listener)`注册监听器
- 



在项目中使用 redux
```javascript
import { connect } from 'react-redux'
import { addNumber, subtractNumber, multiplyNumber } from '../actions'
import Caculation from '../components/Caculation'
const mapStateToProps = state => ({
  caculate: state.caculate
})
const mapDispatchToProps = dispatch => ({
  plus: number => dispatch(addNumber(number)),
  subtract: number => dispatch(subtractNumber(number)),
  multiply: number => dispatch(multiplyNumber(number))
})
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Caculation)
```