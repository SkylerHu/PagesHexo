---
title: Java实现表达式的运算-Android简易计算器
date: 2014-04-05 20:19
tags:
  - Java
  - Android
---
计算一个正确的字符串形式运算式(若要实现计算器，还需对表达式的合法性进行判断)

#### 效果图

![计算器效果图](http://blog-img.skylerhu.com/2016/02/22/1d21dd1111.png)

#### 运算代码CalculatorMathUtil.java

```Java
import java.math.BigDecimal;  
import java.math.MathContext;  
import java.util.Stack;  
  
/**  
 * 计算一个正确的字符串形式运算式 
 * @author HLP 
 * 
 */  
public class CalculatorMathUtil {  
    /** 
     * 数据栈 
     */  
    Stack<BigDecimal> number;  
    /** 
     * 符号栈 
     */  
    Stack<Character> operator;  
    /** 
     * 计算除法时，设置的结果标度 
     */  
    int length;  
  
    public CalculatorMathUtil(int length) {  
        this.length = length;  
    }  
  
    /** 
     * 获得计算结果的字符串表示形式 
     * @param exp 
     * @return 
     */  
    public String getResult(String exp) {  
        this.number = new Stack<BigDecimal>();  
        this.operator = new Stack<Character>();  
        calculate(exp);  
        return this.number.peek().toString();  
    }  
  
    /** 
     * 对运算式exp进行解析，并计算 
     * @param exp 
     */  
    public void calculate(String exp) {  
        int index = 0;  
        int sign = 0;  
        while (index < exp.length()) {  
            char op = exp.charAt(index);  
            if ((op <= '9' && op >= '0') || op == '.') {  
                index++;  
            } else {  
                if (index > 0) {  
                    char opp = exp.charAt(index-1);  
                    if (op == '-' && !((opp <= '9' && opp >= '0') || opp == '.')) {  
                        index++;  
                        continue;  
                    }  
                }  
                if (sign != index) {  
                    String numStr = exp.substring(sign, index);  
                    BigDecimal num = new BigDecimal(numStr);  
                    number.push(num);  
                }  
                analysis(op);  
                sign = ++index;  
            }  
        }  
    }  
  
    /** 
     * 对得到的数据和符号进行分析处理 
     * @param op 
     * @param num 
     */  
    public void analysis(char op) {  
        if (operator.empty() || comparePriority(op, operator.peek())) {  
            operator.push(op);  
        } else {  
            makeNum(op);  
        }  
    }  
  
    /** 
     * 计算 
     * @param op 
     * @param num 
     */  
    private void makeNum(char op) {  
        BigDecimal result = null;  
        while (!operator.empty() && !comparePriority(op, operator.peek())) {  
            char op2 = operator.pop();  
            if (op == ')' && op2 == '(') {  
                return;  
            }  
            BigDecimal num1 = null;  
            BigDecimal num2 = number.pop();  
            if (number.empty()) {  
                num1 = new BigDecimal(0);  
            } else {  
                num1 = number.pop();  
            }  
            switch (op2) {  
            case '+':  
                result = num1.add(num2);  
                break;  
            case '-':  
                result = num1.subtract(num2);  
                break;  
            case '*':  
                result = num1.multiply(num2);  
                break;  
            case '/':  
                result = num1.divide(num2, new MathContext(length));  
                break;  
            default:  
                operator.pop();  
                break;  
            }  
            number.push(result);  
        }  
        if (op != ')') {  
            operator.push(op);  
        }  
    }  
  
    /** 
     * 判断优先级：true(c1优先级高于c2)、false(c1优先级低于c2) 
     * @param c1 
     * @param c2 
     * @return 
     */  
    public boolean comparePriority(char c1, char c2) {  
        if (c1 == '=') {  
            return false;  
        } else if (c1 == ')') {  
            return false;  
        } else if (c1 == '(') {  
            return true;  
        } else if (c2 == '(') {  
            return true;  
        } else if ((c1 == '*' || c1 == '/') && (c2 == '-' || c2 == '+')) {  
            return true;  
        } else {  
            return false;  
        }  
  
    }  
  
}
```

#### CalculatorMainActivity.java

**在输入的同时来保证表达式的合法性**

```Java
import com.anjoyo.lifelittlehelper.R;  
  
import android.os.Bundle;  
import android.app.Activity;  
import android.view.View;  
import android.widget.Button;  
import android.widget.TextView;  
import android.widget.Toast;  
  
public class CalculatorMainActivity extends Activity {  
  
    private TextView mShowTextView = null;  
    private StringBuffer mExpressBuffer = new StringBuffer();  
    private String mResult = "";  
    private static CalculatorMathUtil mCalculatorMathUtil = new CalculatorMathUtil(20);  
  
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_calculator_main);  
  
        mShowTextView = (TextView) findViewById(R.id.tv_calculator_result);  
          
    }  
  
    public void clickMe(View v) {  
        if (mResult.length() > 0 && mExpressBuffer.toString().contains("=")) {  
            mExpressBuffer = new StringBuffer();  
        }  
        Button btn = (Button) v;  
        switch (btn.getId()) {  
        case R.id.btn_calculator_num_0:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(0);  
            break;  
        case R.id.btn_calculator_num_1:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(1);  
            break;  
        case R.id.btn_calculator_num_2:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(2);  
            break;  
        case R.id.btn_calculator_num_3:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(3);  
            break;  
        case R.id.btn_calculator_num_4:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(4);  
            break;  
        case R.id.btn_calculator_num_5:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(5);  
            break;  
        case R.id.btn_calculator_num_6:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(6);  
            break;  
        case R.id.btn_calculator_num_7:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(7);  
            break;  
        case R.id.btn_calculator_num_8:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(8);  
            break;  
        case R.id.btn_calculator_num_9:  
            if (mExpressBuffer.length() != 0 && mExpressBuffer.charAt(mExpressBuffer.length() - 1) == ')') {  
                break;  
            }  
            mExpressBuffer.append(9);  
            break;  
        case R.id.btn_calculator_num_point:  
            if (mExpressBuffer.length() != 0) {  
                char c = mExpressBuffer.charAt(mExpressBuffer.length() - 1);  
                if (c >= '0' && c <= '9') {  
                    mExpressBuffer.append(".");  
                }  
            }  
            break;  
        case R.id.btn_calculator_brackets_left:  
            if (mExpressBuffer.length() == 0) {  
                mExpressBuffer.append("(");  
            } else {  
                char c = mExpressBuffer.charAt(mExpressBuffer.length() - 1);  
                if (c == '+' || c == '-' || c == '*' || c == '/' || c == '(') {  
                    mExpressBuffer.append("(");  
                }  
            }  
            break;  
        case R.id.btn_calculator_brackets_right:  
            if (mExpressBuffer.length() != 0) {  
                char c = '\0';  
                int n1 = 0;// '('的个数  
                int n2 = 0;// ')'的个数  
                for (int i = 0; i < mExpressBuffer.length(); i++) {  
                    c = mExpressBuffer.charAt(i);  
                    if (c == '(') {  
                        n1++;  
                    } else if (c == ')') {  
                        n2++;  
                    }  
                }  
                if (n2 < n1 && (c >= '0' && c <= '9' || c == ')')) {  
                    mExpressBuffer.append(")");  
                }  
            }  
            break;  
        case R.id.btn_calculator_back_space:  
            if (mExpressBuffer.length() != 0) {  
                mExpressBuffer.deleteCharAt(mExpressBuffer.length() - 1);  
            }  
            break;  
        case R.id.btn_calculator_operator_0:  
            mExpressBuffer = new StringBuffer();  
            mResult = "";  
            break;  
        case R.id.btn_calculator_operator_1:  
            if (mResult.length() != 0) {  
                mExpressBuffer.append(mResult);  
                mResult = "";  
            }  
            if (mExpressBuffer.length() == 0) {  
                mExpressBuffer.append("+");  
            } else {  
                char c = mExpressBuffer.charAt(mExpressBuffer.length() - 1);  
                if (c >= '0' && c <= '9' || c == ')') {  
                    mExpressBuffer.append("+");  
                }  
            }  
            break;  
        case R.id.btn_calculator_operator_2:  
            if (mResult.length() != 0) {  
                mExpressBuffer.append(mResult);  
                mResult = "";  
            }  
            if (mExpressBuffer.length() == 0) {  
                mExpressBuffer.append("-");  
            } else {  
                int len = mExpressBuffer.length();  
                char c = mExpressBuffer.charAt(len - 1);  
                if (c >= '0' && c <= '9' || c == '(' || c == ')') {  
                    mExpressBuffer.append("-");  
                } else if (len >= 2) {  
                    c = mExpressBuffer.charAt(len - 2);  
                    if (c >= '0' && c <= '9' || c == '(' || c == ')') {  
                        mExpressBuffer.append("-");  
                    }  
                }  
            }  
            break;  
        case R.id.btn_calculator_operator_3:  
            if (mResult.length() != 0) {  
                mExpressBuffer.append(mResult);  
                mResult = "";  
            }  
            if (mExpressBuffer.length() != 0) {  
                char c = mExpressBuffer.charAt(mExpressBuffer.length() - 1);  
                if (c >= '0' && c <= '9' || c == ')') {  
                    mExpressBuffer.append("*");  
                }  
            }  
            break;  
        case R.id.btn_calculator_operator_4:  
            if (mResult.length() != 0) {  
                mExpressBuffer.append(mResult);  
                mResult = "";  
            }  
            if (mExpressBuffer.length() != 0) {  
                char c = mExpressBuffer.charAt(mExpressBuffer.length() - 1);  
                if (c >= '0' && c <= '9' || c == ')') {  
                    mExpressBuffer.append("/");  
                }  
            }  
            break;  
        case R.id.btn_calculator_operator_5:  
            if (mExpressBuffer.length() != 0) {  
                char c = '\0';  
                int n1 = 0;// '('的个数  
                int n2 = 0;// ')'的个数  
                for (int i = 0; i < mExpressBuffer.length(); i++) {  
                    c = mExpressBuffer.charAt(i);  
                    if (c == '(') {  
                        n1++;  
                    } else if (c == ')') {  
                        n2++;  
                    }  
                }  
                if (n1 == n2 && (c >= '0' && c <= '9' || c == ')')) {  
                    mExpressBuffer.append("=");  
                    try {  
                        mResult = mCalculatorMathUtil.getResult(mExpressBuffer.toString());  
                        mExpressBuffer.append(mResult);  
                    } catch (Exception e1) {  
//                      mExpressBuffer.append("  IS ERROR");  
                        mExpressBuffer.deleteCharAt(mExpressBuffer.length() - 1);  
                        Toast.makeText(this, "表达式错误", Toast.LENGTH_LONG).show();  
                    }  
  
                }  
            }  
            break;  
        default:  
            break;  
        }  
        mShowTextView.setText(mExpressBuffer.toString());  
    }  
  
}
```

#### 布局

```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:background="#ff000000"  
    android:orientation="vertical" >  
      
    <TextView  
        android:id="@+id/tv_calculator_result"  
        android:layout_width="match_parent"  
        android:layout_height="100dp"  
        android:background="#cccccccc"  
        android:textSize="26sp"  
        android:gravity="bottom|right"  
        android:layout_margin="10dp"  
        android:paddingBottom="10dp"  
        android:paddingLeft="5dp"  
        android:paddingRight="5dp"  
        />  
    <TableLayout   
        android:id="@+id/tl_calculator_keys_layout"  
        android:layout_width="fill_parent"  
        android:layout_height="wrap_content"  
        android:stretchColumns="0,1,2,3"  
        android:layout_marginTop="10dp"  
        android:layout_marginLeft="5dp"  
        android:layout_marginRight="5dp"  
        >  
        <TableRow >  
            <Button   
                android:id="@+id/btn_calculator_brackets_left"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/brackets0"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_brackets_right"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/brackets1"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_operator_0"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operator0"  
                android:background="@drawable/calculator_button_background_2"  
                android:onClick="clickMe"  
                />  
            <Button  
                android:id="@+id/btn_calculator_back_space"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operatorC"   
                android:background="@drawable/calculator_button_background_2"  
                android:onClick="clickMe"  
                />  
        </TableRow>  
          
        <TableRow >  
            <Button   
                android:id="@+id/btn_calculator_operator_1"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operator1"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_7"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num7"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_8"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num8"  
                android:onClick="clickMe"  
                />  
            <Button  
                android:id="@+id/btn_calculator_num_9"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num9"   
                android:onClick="clickMe"  
                />  
        </TableRow>  
          
        <TableRow >  
            <Button   
                android:id="@+id/btn_calculator_operator_2"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operator2"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_4"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num4"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_5"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num5"  
                android:onClick="clickMe"  
                />  
            <Button  
                android:id="@+id/btn_calculator_num_6"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num6"   
                android:onClick="clickMe"  
                />  
        </TableRow>  
          
        <TableRow >  
            <Button   
                android:id="@+id/btn_calculator_operator_3"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operator3"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_1"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num1"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_2"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num2"  
                android:onClick="clickMe"  
                />  
            <Button  
                android:id="@+id/btn_calculator_num_3"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num3"   
                android:onClick="clickMe"  
                />  
        </TableRow>  
          
        <TableRow >  
            <Button   
                android:id="@+id/btn_calculator_operator_4"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operator4"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_point"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/point"  
                android:onClick="clickMe"  
                />  
            <Button   
                android:id="@+id/btn_calculator_num_0"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/num0"  
                android:onClick="clickMe"  
                />  
            <Button  
                android:id="@+id/btn_calculator_operator_5"  
                style="@style/calculator_button_style"  
                android:layout_margin="1dp"  
                android:layout_weight="0.25"  
                android:text="@string/operator5"   
                android:background="@drawable/calculator_button_background_2"  
                android:onClick="clickMe"  
                />  
        </TableRow>  
          
    </TableLayout>  
  
</LinearLayout>
```

#### 样式

```xml
<style name="calculator_button_style">  
        <item name="android:layout_width">68dp</item>  
        <item name="android:layout_height">50dp</item>  
        <item name="android:gravity">center</item>  
        <item name="android:textSize">18sp</item>  
        <item name="android:textColor">#ffffffff</item>  
        <item name="android:textStyle">bold</item>  
        <item name="android:background">@drawable/calculator_button_background</item>  
</style> 
```

有个可查看的Android项目，[请点击查看源码](https://github.com/SkylerHu/Calculator)
