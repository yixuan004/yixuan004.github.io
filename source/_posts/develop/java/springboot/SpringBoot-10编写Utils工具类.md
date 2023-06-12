---
title: SpringBoot-10编写Utils工具类
date: 2023-06-09 00:35:16
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-10编写Utils工具类

在任何项目中，工具类都是十分关键的处理方式，工具类下面提供了不同的方法，例如在本项目现阶段中，`PinyinUtils`类负责完成将字符串转化成拼音的方式进行一些处理，`ScoreUtils`类负责进行各种编辑距离的计算

需要注意的是，这里的工具类一般直接使用就可以，里面大多都是私有属性静态方法这样的直接供外部使用，如果需要初始化（例如RPC那种要先初始化一个Client的，就需要用到设计模式的知识了）

utils文件夹和controller service config这些文件夹都是平级的

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/utils/PinyinUtils.java`

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/utils/ScoreUtils.java`

## 1. PinyinUtils

```java
package cn.edu.bupt.aiswitchboard.utils;

import cn.edu.bupt.aiswitchboard.exceptions.NotImplementedException;
import com.hankcs.hanlp.dictionary.py.Pinyin;
import com.hankcs.hanlp.dictionary.py.PinyinDictionary;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class PinyinUtils {

    public static String getPinyin(String chineseStr, String type){
        StringBuilder sb = new StringBuilder();
        List<Pinyin> basePinyins = PinyinDictionary.convertToPinyin(chineseStr);
        if (type.equals("withTone")) {
            for (Pinyin basePinyin : basePinyins) {
                sb.append(basePinyin.getPinyinWithToneMark()).append(" ");
            }
        } else if (type.equals("withoutTone")){
            for (Pinyin basePinyin : basePinyins) {
                sb.append(basePinyin.getPinyinWithoutTone()).append(" ");
            }
        } else {
            throw new NotImplementedException();
        }
//        System.out.println(pinyins);
        return sb.toString();
    }

    public static String toMandarinSet(String x) {
        Pattern pattern = Pattern.compile("((?<!\\w)"
                + "(zh|ch|sh|y|w|b|p|m|f|d|t|l|n|g|k|h|j|q|x|z|c|s|r)?"
                + "(ang|eng|ing|ong|uan|uang|uai|iang|ian|iao|"
                + "ai|ei|ui|ao|ou|iv|ie|ve|ua|ue|uo|er|an|en|in|un|vn|"
                + "a|o|e|i|u|v)"
                + "(?!\\w))");
        Matcher matcher = pattern.matcher(x);
        Set<String> st = new HashSet<>();

        String[] fuzzyMand = {"zh", "sh", "ch", "ang", "eng", "ing"};
        String[] fuzzyMandReplacements = {"z", "s", "c", "an", "en", "in"};

        while (matcher.find()) {
            String a = matcher.group(1);
            String b = matcher.group(2);
            String c = matcher.group(3);

            for (int i = 0; i < fuzzyMand.length; i++) {
                a = a.replace(fuzzyMand[i], fuzzyMandReplacements[i]);
                b = b.replace(fuzzyMand[i], fuzzyMandReplacements[i]);
                c = c.replace(fuzzyMand[i], fuzzyMandReplacements[i]);
            }
            st.add(a);
            st.add(c);
        }

        StringBuilder sb = new StringBuilder();
        for (String str: st) {
            sb.append(str).append(" ");
        }
        return sb.toString();
    }

}
```

## 2. ScoreUtils

```java
package cn.edu.bupt.aiswitchboard.utils;

import org.apache.commons.text.similarity.LevenshteinDistance;

import java.util.Arrays;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class ScoreUtils {


    // 直接计算编辑距离，
    public static double basicEditDistance(String s1, String s2) {
        int distance = LevenshteinDistance.getDefaultInstance().apply(s1, s2);
        return 1 - (double) distance / Math.max(s1.length(), s2.length());
    }

    // 两个字符串按照空格分割，先转成list后
    public static double strHashEditDistance(String s1, String s2) {
        String[] s1List = s1.split(" ");
        String[] s2List = s2.split(" ");

//        System.out.println(s1);
//        System.out.println(s2);

        StringBuilder sb1 = new StringBuilder();
        for (int i = 0; i < s1List.length; i++) {
            char c = (char) (s1List[i].hashCode() % 0x10ffff);
            sb1.append(c);
        }
        StringBuilder sb2 = new StringBuilder();
        for (int i = 0; i < s2List.length; i++) {
            char c = (char) (s2List[i].hashCode() % 0x10ffff);
            sb2.append(c);
        }

        String s1Hash = sb1.toString();
        String s2Hash = sb2.toString();

        int distance = LevenshteinDistance.getDefaultInstance().apply(s1Hash, s2Hash);
        return 1 - (double) distance / Math.max(s1Hash.length(), s2Hash.length());
    }

    public static double setScore(String s1, String s2) {
        String[] s1List = s1.split(" ");
        String[] s2List = s2.split(" ");

        Set<String> set1 = new HashSet<>(Arrays.asList(s1List));
        Set<String> set2 = new HashSet<>(Arrays.asList(s2List));

        Set<String> intersection = new HashSet<>(set1);
        intersection.retainAll(set2);
        int intersectionSize = intersection.size();

        Set<String> union = new HashSet<>(set1);
        union.addAll(set2);
        int unionSize = union.size();

//        System.out.println("交集大小: " + intersectionSize);
//        System.out.println("并集大小: " + unionSize);
//        System.out.println("交集大小与并集大小的比例: " + (double) intersectionSize / unionSize);
        return (double) intersectionSize / unionSize;
    }
}
```

