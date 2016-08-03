---
title: TextView FontSize Util
---

## 一个调整字体大小的工具类 ##
非原创：可以自己拓展
```java
public class FontUtil {

    /**
     * 为textview的fontsize设置一下，这里是再原来的基础上增大
     * @param textView
     * @param context
     * @param delaFontSize
     */
    public static void tweakFontSize(TextView textView, Context context, float delaFontSize ){
            textView.setTextSize(TypedValue.COMPLEX_UNIT_PX, textView.getTextSize() + PhoneUtil.sp2px(context, delaFontSize) );
    }


    /**
     * 将activity里所有的textview的fontsize设置一下
     * @param activity
     * @param delaFontSize
     */
    public static void tweakFontSize(Activity activity,float delaFontSize){
        ViewGroup root = (ViewGroup)activity.findViewById(android.R.id.content);
        tweakFontSize( root, activity, fontSize);
    }


    /**
     * 将某个rootview下的fontsize都调整一下。
     * 注意在adpater里使用的时候，只能调整一次
     * @param rootView
     * @param activity
     * @param delaFontSize
     */
    public static void tweakFontSize(ViewGroup rootView, Activity activity, float delaFontSize){
        ViewTraverse t = new ViewTraverse(rootView);
        TextView tv = null;
        while( ( tv=t.nextTextView() ) != null ){
            tweakFontSize(tv, activity, fontSize);
        }

    }


    private static class ViewTraverse{
        private LinkedList<ViewGroup> views = new LinkedList<ViewGroup>();
        private ViewGroup curViewGroup;
        private int childPos;

        public ViewTraverse(ViewGroup v){
            views.push(v);
        }

        public View next(){

            if( curViewGroup == null || childPos>=curViewGroup.getChildCount() ){
                if( views.size()!=0 ){
                    curViewGroup = views.pop();
                    childPos = 0;
                }else{
                    return null;
                }
            }

            if( curViewGroup.getChildCount() == 0 ){
                curViewGroup = null;
                return next();
            }else{
                View v = curViewGroup.getChildAt(childPos);
                if( v instanceof ViewGroup){
                    views.push((ViewGroup)v);
                }
                childPos++;
                return v;
            }
        }

        public TextView nextTextView(){
            View v;
            while( (v=next())!=null ){
                if( v instanceof TextView){
                    return (TextView)v;
                }
            }
            return (TextView)v;
        }


    }


}
```
