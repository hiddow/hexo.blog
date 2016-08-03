---
title: Retrieve a Fragment from a ViewPager
---

## 问题描述 ##

我们有一个ViewPager 里面有三个Fragment
> * Fragment1
> * Fragment2
> * Fragment3

如果我们要在获取其中一个**Fragment**实例要怎么做？

### 一般情况下我们会这么做： ###

之前我的写法：

```java

private List<Fragment> mFragments = new ArrayList<>();
private List<String> mFragmentTitles = new ArrayList<>();

private class TabIndexAdapter extends FragmentPagerAdapter {

        public TabIndexAdapter(FragmentManager fm) {
            super(fm);
            mFragments.clear();
            mFragmentTitles.clear();
        }

        public void addFragment(android.support.v4.app.Fragment fragment, String title) {
            mFragments.add(fragment);
            mFragmentTitles.add(title);
        }

        @Override
        public android.support.v4.app.Fragment getItem(int position) {
            return mFragments.get(position);
        }

        @Override
        public int getCount() {
            return mFragments.size();
        }

        @Override
        public CharSequence getPageTitle(int position) {
            return mFragmentTitles.get(position);
        }

}

TabIndexAdapter mAdp = new TabIndexAdapter(getSupportFragmentManager());
mAdp.addFragment(new LessonSpeakFragment(), getString(R.string.Speak));
mAdp.addFragment(new LessonReadFragment(), getString(R.string.Read));
mAdp.addFragment(new LessonListenFragment(), getString(R.string.Listen));
mAdp.addFragment(new LessonDictationFragment(), getString(R.string.Dictation));
viewPager.setAdapter(mAdp);

```
然后这么获取：

```java
LessonSpeakFragment = (LessonSpeakFragment)mFragments.get(0);    
```
顿时觉得我好聪明啊
可是这么写是有问题的！！！
> 因为我们没有考虑内存回收的问题
> 也就是说当当前页面被系统意外回收然后再恢复的时候
> 这个时候需要注意了,因为此时我们mFragments会重新创建，添加进去新的Fragment实例
> 但是！这个时候FragmentPagerAdapter 会获取之前自动保存的Fragment实例，而不是重新getItem()!
> 所以问题就来，这个时候按照之前我的方法，得到的和现在展示的Fragment就不是同一个实例了！

## 解决方案 ##

### 一个比较简单的方法 ：ViewPager.instantiateItem() ###

### 另一个可行的方法 ：重写adapter ###
```java
private class TabIndexAdapter extends FragmentPagerAdapter {

        private Class[] fragmentClasses = new Class[]{
                PodLevelFragment.class,
                PodCategoryFragment.class,
                PodAllFragment.class};
        private int[] fragmetTitles = new int[]{
                R.string.LEVEL,
                R.string.CATEGORY,
                R.string.PODCAST_ALL
        };


        private SparseArray<Fragment> registeredFragments = new SparseArray<Fragment>();

        public Fragment getRegisteredFragment(int position) {
            return registeredFragments.get(position);
        }

        @Override
        public Object instantiateItem(ViewGroup container, int position) {
            Fragment fragment = (Fragment) super.instantiateItem(container, position);
            registeredFragments.put(position, fragment);
            return fragment;
        }


        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            registeredFragments.remove(position);
            super.destroyItem(container, position, object);
        }


        public TabIndexAdapter(FragmentManager fm) {
            super(fm);

        }

        @Override
        public android.support.v4.app.Fragment getItem(int position) {
            Log.i("TabLearnPage", "getItem : "+position);
            try{
                return (android.support.v4.app.Fragment)fragmentClasses[position].newInstance();
            }catch(Exception e){
                throw new RuntimeException(e);
            }
        }

        @Override
        public int getCount() {
            return fragmentClasses.length;
        }

        @Override
        public CharSequence getPageTitle(int position) {
            return getString(fragmetTitles[position]);
        }

}
```
直接调用adapter 的 **getRegisteredFragment()**方法就Ok啦！










