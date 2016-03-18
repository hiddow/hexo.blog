---
title: Popwindow 出现时对键盘的处理
---

有的时候弹出Popwindow 的时候，Android 键盘不会自动隐藏，导致界面不是理想情况，并且即使键盘消失了，Popwindow 的布局也会被打乱，大神写了一个针对Popwindow 有效的显示和隐藏键盘的工具类:
```android
/**
 * 隐藏/开启keyboard
 *
 * http://glblong.blog.51cto.com/3058613/1543172
 *
 * Created by zougang on 15/9/30.
 */
public class KeyboardUtil {

    /**
     * 处理点击Edit，自动弹出来的keyboard的情况
     * @param context
     */
    public static void hideFocusedKeyboard(Activity context){
        //确保键盘消失
        InputMethodManager manager = (InputMethodManager) context.getSystemService(Context.INPUT_METHOD_SERVICE);
        if (context.getCurrentFocus() != null && context.getCurrentFocus().getWindowToken() != null){
            manager.hideSoftInputFromWindow(context.getCurrentFocus().getWindowToken(), InputMethodManager.HIDE_NOT_ALWAYS);
        }
    }


    /**
     * 强制显示keyboard，不用touch去处理。
     *
     * @param context
     * @param popupWindow
     */
    public static void showPopupKeboard(Activity context, PopupWindow popupWindow){
        InputMethodManager inputMgr = (InputMethodManager)context.getSystemService(Context.INPUT_METHOD_SERVICE);
        inputMgr.toggleSoftInput(InputMethodManager.SHOW_FORCED, 0);// 调用此方法才能自动打开输入法软键盘
        popupWindow.setSoftInputMode(
                WindowManager.LayoutParams.SOFT_INPUT_STATE_VISIBLE | WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);
        popupWindow.setInputMethodMode(PopupWindow.INPUT_METHOD_NEEDED); // 在显示popupwindow之后调用，否则输入法会在窗口底层
    }

    /**
     * 隐藏输入法软键盘，对popupwindow有效的<br>
     * 强制隐藏keyboard
     * @param context
     * @param popupWindow
     */
    public static void hidePopupKeyboard(Activity context, PopupWindow popupWindow)
    {
        InputMethodManager inputMgr = (InputMethodManager) context.getSystemService(Context.INPUT_METHOD_SERVICE);
        inputMgr.toggleSoftInput(InputMethodManager.HIDE_NOT_ALWAYS, 0);// 输入法软键盘打开时关闭,关闭时打开
        popupWindow.setSoftInputMode(
                WindowManager.LayoutParams.SOFT_INPUT_STATE_ALWAYS_HIDDEN | WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);
        popupWindow.setInputMethodMode(PopupWindow.INPUT_METHOD_NEEDED); // 在显示popupwindow之后调用，否则输入法会在窗口底层

        //此方法无效
        //        if(this.getCurrentFocus().getWindowToken() != null)
//        {
//            ((InputMethodManager) getSystemService(INPUT_METHOD_SERVICE)).hideSoftInputFromWindow(this.getCurrentFocus().getWindowToken(),
//                    InputMethodManager.HIDE_NOT_ALWAYS);// 关闭输入法软键盘
//        }

    }

}

```




