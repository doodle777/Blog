---
layout: post
date: 2015-10-18 17:18:00
title:  "Android开发手记(10) 下拉菜单Spinner"
category: [Android]
tags:   [Android, Spinner]
---

* content
{:toc}

#### **1、自定义Spinner**

　　首先，定义Spinner要显示的项目列表/res/values/arrays.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="citys">
        <item>南京</item>
        <item>徐州</item>
        <item>常州</item>
    </string-array>
</resources>
```

　　然后，定义一个ArrayAdapter<String>并绑定到上面定义的项目列表。

```java
ArrayAdapter<String> adapter = ArrayAdapter.createFromResource(
                 this, R.array.citys, android.R.layout.simple_spinner_item);
adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
```

　　其中，createFromResource()的函数原型为：

> createFromResource(Context context, int textArrayResId, int textViewResId);

　　然后，将adapter绑定到spinner：

```java
        spinner.setAdapter(adapter);
```

　　最后，为spinner添加一个setOnItemSelectedListener即可：

```java
        spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                Toast.makeText(MainActivity.this, "position" + i + spinner.getSelectedItem().toString(),
                            Toast.LENGTH_SHORT).show();
                }

            @Override
            public void onNothingSelected(AdapterView<?> adapterView) {
                Toast.makeText(MainActivity.this, "unselected", Toast.LENGTH_SHORT);
            }
        });
```

　　完整代码为：

```java
public class MainActivity extends AppCompatActivity {

    private Spinner spinner;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        spinner = (Spinner) findViewById(R.id.spinner);

        ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
                this, R.array.citys, android.R.layout.simple_spinner_item);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        spinner.setAdapter(adapter);

        spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                Toast.makeText(MainActivity.this, "position" + i + spinner.getSelectedItem().toString(),
                            Toast.LENGTH_SHORT).show();
                }

            @Override
            public void onNothingSelected(AdapterView<?> adapterView) {
                Toast.makeText(MainActivity.this, "unselected", Toast.LENGTH_SHORT);
            }
        });

    }

}
```

#### **2、动态改变Spinner**

　　想动态改变Spinner的内容，需要将Spinner的内容绑定到一个Adapter上。通过增加ArrayAdapter的内容来动态改变Spinner的内容。

```java
         ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_spinner_dropdown_item);
         adapter.add("南京");
         adapter.add("徐州");
         adapter.add("常州");
         spinner.setAdapter(adapter);
```

#### （2.1）增加一个项

　　通过在EditText输入需要增加的内容，之后单击一个Button来增加Spinner。为Button添加以下事件即可。

```java
        btnAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                String city = editText.getText().toString();
                for (int i = 0; i < adapter.getCount(); i++) {
                    if (city.equals(adapter.getItem(i))) {
                        Toast.makeText(MainActivity.this, "此城市已存在", Toast.LENGTH_SHORT).show();
                        return;
                    }
                }
                if (!city.trim().equals("")) {
                    adapter.add(city);
                    int pos = adapter.getPosition(city);
                    spinner.setSelection(pos);
                    editText.setText("");
                }
            }
        });
```

#### （2.2）删除一个项

　　通过在Spinner选中需要删除的内容，之后单击一个Button来删除Spinner。为Button添加以下事件即可。

```java
        btnDel.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (spinner.getSelectedItem() != null) {
                    adapter.remove(spinner.getSelectedItem().toString());
                    editText.setText("");
                    if (adapter.getCount() == 0) {
                        Toast.makeText(MainActivity.this, "列表为空", Toast.LENGTH_SHORT).show();
                    }
                }
            }
        });
```

#### **3、完整代码如下：**

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    private Spinner spinner;
    private EditText editText;
    private Button btnAdd;
    private Button btnDel;
    private ArrayAdapter<String> adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnAdd = (Button) findViewById(R.id.btnAdd);
        btnDel = (Button) findViewById(R.id.btnDel);
        editText = (EditText) findViewById(R.id.editText);
        spinner = (Spinner) findViewById(R.id.spinner);

        adapter = new ArrayAdapter<String>(this, android.R.layout.simple_spinner_dropdown_item);
        adapter.add("南京");
        adapter.add("徐州");
        adapter.add("常州");
        spinner.setAdapter(adapter);

        btnAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                String city = editText.getText().toString();
                for (int i = 0; i < adapter.getCount(); i++) {
                    if (city.equals(adapter.getItem(i))) {
                        Toast.makeText(MainActivity.this, "此城市已存在", Toast.LENGTH_SHORT).show();
                        return;
                    }
                }
                if (!city.trim().equals("")) {
                    adapter.add(city);
                    int pos = adapter.getPosition(city);
                    spinner.setSelection(pos);
                    editText.setText("");
                }
            }
        });
        btnDel.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (spinner.getSelectedItem() != null) {
                    adapter.remove(spinner.getSelectedItem().toString());
                    editText.setText("");
                    if (adapter.getCount() == 0) {
                        Toast.makeText(MainActivity.this, "列表为空", Toast.LENGTH_SHORT).show();
                    }
                }
            }
        });
        spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                editText.setText(adapter.getItem(i));
            }

            @Override
            public void onNothingSelected(AdapterView<?> adapterView) {

            }
        });

    }

}
```
 
