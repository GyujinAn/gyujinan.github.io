---
layout: post
title: "버블정렬이란?"
date: 2022-01-21
categories: algorithm
---

# 버블정렬이란?

정렬은 데이터에 대한 검색을 효율적으로 하기 위해서 교환, 선택, 삽입을 사용하여 오름차순(ascending order) 혹은 내림차순(descending order)으로 데이터를 나열하는 것이다. 검색에 대한 효율성이 향상되기 때문에 알고리즘 시간복잡도에 큰 영향을 줄 수 있다. 정렬 알고리즘은 여러가지가 있는데 이번 포스팅에서는 오름차순의 버블 정렬에 대해 포스팅한다.

* [소스코드](https://github.com/GyujinAn/algorithm/blob/main/src/bubblesort/Main.java)

## Pass

버블정렬은 pass의 모임이므로 pass에 대하여 먼저 알아본다. pass란 배열 a의 크기가 n이라고 하고 배열 첫번째 인덱스를 j이라고 할때, a의 가장 뒤에 두수 a[n-1], a[n-2]부터 가장 앞의 두수 a[j], a[j+1]까지 차례대로 선택 및 교환하여 가장 작은 수를 a[j]로 옮기는 행위이다. 행위를 반대로 하여 가장 앞에서부터 비교 할 수도 있고 내림차순이라면 가장 큰수를 a[j]로 옮길 수도 있다. 가장 작은 수를 가장 앞으로 옮기는 예시와 가장 큰수를 가장 뒤로 옮기는 코드를 만들어 본다.


```
class Main {


    //가장 작은 수를 앞으로 옮기는 pass
    private static int[] pass0(int[] a, int n){
        for(int i=n-1; i>0; i--){
            if(a[i] < a[i-1]){
                swap(a, i, i-1);
            }
        }
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass
    private static int[] pass1(int[] a, int n){
        for(int i=0; i<n-1; i++){
            if(a[i] > a [i+1]){
                swap(a, i, i+1);
            }
        }
        return a;
    }

    private static void printArray(int[] a){
        for(int tmp : a){
            System.out.print(tmp+" ");
        }
        System.out.println();
    }

    private static void swap(int a[], int i0, int i1){
        int tmp = a[i0];
        a[i0] = a[i1];
        a[i1] = tmp;
    }


    public static void main(String[] args) {

        int[] array;

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array0 before pass0===");
        printArray(array);
        pass0(array, array.length);
        System.out.println("===array0 after pass0===");
        printArray(array);

        System.out.println();

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array1 before pass1===");
        printArray(array);
        pass1(array, array.length);
        System.out.println("===array1 after pass1===");
        printArray(array);

    }

}

-----출력 결과-----

===array0 before pass0===
6 4 3 7 1 9 8 
===array0 after pass0===
1 6 4 3 7 8 9 

===array1 before pass1===
6 4 3 7 1 9 8 
===array1 after pass1===
4 3 6 1 7 8 9 

```

출력 결과를 살펴보면 첫번째 pass는 가장 작은 수 1이 가장 앞으로 두번째 pass는 가장 큰수 9가 가장 뒤로 이동한 것을 확인 할 수 있다.

## 버블 정렬

전 pass의 최소수 혹은 최대수를 제외하고 pass를 n-1번 반복하면 배열은 최종적으로 정렬이 된다. 버블 정렬을 완성하여 본다.
```
class Main {

    //가장 작은 수를 앞으로 옮기는 pass로 구성 된 버블 정렬
    private static int[] bubbleSort0(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        for(int j=0; j<n-1; j++){
            System.out.println("---"+"pass"+passCount+"---");
            for(int i=n-1; i>j; i--){
                if(a[i] <  a[i-1]){
                    swap(a, i, i-1);
                    swapCount++;
                }
                compareCount++;
            }
            printArray(a);
            passCount++;
        }
        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass로 구성 된 버블 정렬
    private static int[] bubbleSort1(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        for(int j=n-1; j>0; j--){
            System.out.println("---"+"pass"+passCount+"---");
            for(int i=0; i<j; i++){
                if(a[i] > a[i+1]){
                    swap(a, i, i+1);
                    swapCount++;
                }
                compareCount++;
            }
            printArray(a);
            passCount++;
        }
        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    private static void printArray(int[] a){
        for(int tmp : a){
            System.out.print(tmp+" ");
        }
        System.out.println();
    }

    private static void swap(int a[], int i0, int i1){
        int tmp = a[i0];
        a[i0] = a[i1];
        a[i1] = tmp;
    }

    public static void main(String[] args) {

        int[] array;

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array2 before bubbleSort0===");
        printArray(array);
        bubbleSort0(array, array.length);
        System.out.println("===array2 after bubbleSort0===");
        printArray(array);

        System.out.println();

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array3 before bubbleSort3===");
        printArray(array);
        bubbleSort1(array, array.length);
        System.out.println("===array3 after bubbleSort3===");
        printArray(array);

    }

}

-----출력 결과-----

===array2 before bubbleSort0===
6 4 3 7 1 9 8 
---pass0---
1 6 4 3 7 8 9 
---pass1---
1 3 6 4 7 8 9 
---pass2---
1 3 4 6 7 8 9 
---pass3---
1 3 4 6 7 8 9 
---pass4---
1 3 4 6 7 8 9 
---pass5---
1 3 4 6 7 8 9 
passCount: 6, compareCount: 21, swapCount: 8
===array2 after bubbleSort0===
1 3 4 6 7 8 9 

===array3 before bubbleSort1===
6 4 3 7 1 9 8 
---pass0---
4 3 6 1 7 8 9 
---pass1---
3 4 1 6 7 8 9 
---pass2---
3 1 4 6 7 8 9 
---pass3---
1 3 4 6 7 8 9 
---pass4---
1 3 4 6 7 8 9 
---pass5---
1 3 4 6 7 8 9 
passCount: 6, compareCount: 21, swapCount: 8
===array3 after bubbleSort1===
1 3 4 6 7 8 9 

```

출력 결과를 살펴보면 배열 array2, array3모두 정렬 된 것을 확인 할 수 있다. 연산의 횟수는 패스는 6번 비교는 21번 교환은 8번 발생하였다.

## 시간복잡도

버블정렬의 시간 복잡도를 계산해본다.

* pass의 총 횟수는 n-1번 발생한다.

* pass안에서의 선택 및 교환 연산은 최초에는 n-1번 발생하고 그 후에는 1씩 감소하면서 발생한다.

이를 식으로 나타내어 본다.
```
버블정렬의 시간복잡도 T(n)에 대해서

T(n) = (n-1) + (n-2) + ... + 2 + 1

가우스의 덧셈 공식을 적용

T(n) = 0.5 *(n^2 - n)

빅오표기법으로는 O(n^2)이다

```

## 버블정렬 업그레이드 1

버블 정렬의 패스 도중에 교환이 한번도 발생하지 않는다면 해당 배열은 정렬이 이미 완료되었다고 할 수 있다. 에를 들어 배열이 1,3,4,6,7,8,9로 구성되었다고 해보자. 해당 배열에서 버블 정렬을 실행시키면 첫번째 패스부터 교환은 한번도 발생하지 않을 것이다. 하지만 패스 n-1번 즉 6번 발생한다. 불필요한 패스가 발생한 것이다. 패스 도중 교환이 발생하지 않았음을 기록하여 불필요한 패스에 대한 비용을 줄일수 있다.
```
class Main {
    //가장 작은 수를 앞으로 옮기는 pass로 구성 된 버블 정렬
    private static int[] bubbleSort0(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        for(int j=0; j<n-1; j++){
            System.out.println("---"+"pass"+passCount+"---");
            for(int i=n-1; i>j; i--){
                if(a[i] <  a[i-1]){
                    swap(a, i, i-1);
                    swapCount++;
                }
                compareCount++;
            }
            printArray(a);
            passCount++;
        }
        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass로 구성 된 버블 정렬
    private static int[] bubbleSort1(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        for(int j=n-1; j>0; j--){
            System.out.println("---"+"pass"+passCount+"---");
            for(int i=0; i<j; i++){
                if(a[i] > a[i+1]){
                    swap(a, i, i+1);
                    swapCount++;
                }
                compareCount++;
            }
            printArray(a);
            passCount++;
        }
        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    //가장 작은 수를 앞으로 옮기는 pass로 구성 된 버블 정렬
    //교환이 발생하지 않은 경우는 정렬이 완료되었으므로 정렬을 종료시키는 업그레이드 버전
    private static int[] upgradedBubbleSort0(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        for(int j=0; j<n-1; j++){
            System.out.println("---"+"pass"+passCount+"---");
            boolean isSwap = false;
            for(int i=n-1; i>j; i--){
                if(a[i] <  a[i-1]){
                    swap(a, i, i-1);
                    swapCount++;
                    isSwap = true;
                }
                compareCount++;

            }
            printArray(a);
            passCount++;
            if(!isSwap) break;
        }
        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass로 구성 된 버블 정렬
    //교환이 발생하지 않은 경우는 정렬이 완료되었으므로 정렬을 종료시키는 업그레이드 버전
    private static int[] upgradedBubbleSort1(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        for(int j=n-1; j>0; j--){
            System.out.println("---"+"pass"+passCount+"---");
            boolean isSwap = false;
            for(int i=0; i<j; i++){
                if(a[i] > a[i+1]){
                    swap(a, i, i+1);
                    swapCount++;
                    isSwap = true;
                }
                compareCount++;
            }
            printArray(a);
            passCount++;
            if(!isSwap) break;
        }
        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }


    private static void printArray(int[] a){
        for(int tmp : a){
            System.out.print(tmp+" ");
        }
        System.out.println();
    }

    private static void swap(int a[], int i0, int i1){
        int tmp = a[i0];
        a[i0] = a[i1];
        a[i1] = tmp;
    }

    public static void main(String[] args) {

        int[] array;

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array4 before upgradedBubbleSort0===");
        printArray(array);
        upgradedBubbleSort0(array, array.length);
        System.out.println("===array4 after upgradedBubbleSort0===");
        printArray(array);

        System.out.println();

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array5 before upgradedBubbleSort1===");
        printArray(array);
        upgradedBubbleSort1(array, array.length);
        System.out.println("===array5 after upgradedBubbleSort1===");
        printArray(array);

    }

}

-----출력 결과-----

===array4 before upgradedBubbleSort0===
6 4 3 7 1 9 8 
---pass0---
1 6 4 3 7 8 9 
---pass1---
1 3 6 4 7 8 9 
---pass2---
1 3 4 6 7 8 9 
---pass3---
1 3 4 6 7 8 9 
passCount: 4, compareCount: 18, swapCount: 8
===array4 after upgradedBubbleSort0===
1 3 4 6 7 8 9 

===array5 before upgradedBubbleSort1===
6 4 3 7 1 9 8 
---pass0---
4 3 6 1 7 8 9 
---pass1---
3 4 1 6 7 8 9 
---pass2---
3 1 4 6 7 8 9 
---pass3---
1 3 4 6 7 8 9 
---pass4---
1 3 4 6 7 8 9 
passCount: 5, compareCount: 20, swapCount: 8
===array5 after upgradedBubbleSort1===
1 3 4 6 7 8 9 

```

위의 실행 결과를 보면 bubbleSort0의 패스는 6회이지만 upgradedBubbleSort0은 4회 발생하였고 bubbleSort1의 패스는 6회 발생하였지만 upgradedBubbleSort0은 5회 발생하였다. 결과 시간복잡도는 개선되었다. (하지만 빅오표기법으로는 최악의 상황을 가정하기에 빅오표기법의 시간복잡도는 이전과 같다.)


## 버블정렬 업그레이드 2

버블정렬 업그레이드 1은 한번의 패스가 완료되어야 더 이상 정렬이 필요한지 필요하지 않은지 판단 할 수 있고 패스의 결과 배열의 모든 구성요소가 정렬되어야만 정렬을 멈춘다. 하지만 패스 도중 배열의 일부는 이미 정렬이 완료되었을 수 있다. 정렬이 완료 된 배열의 일부는 더 이상 정렬 할 필요가 없다. 예를 들어 {1,3,9,4,7,8,6} 이라는 배열의 첫번째 패스의 결과는 {1,3,4,9,6,7,8}이다. 첫번째 패스의 목표는 1을 가장 앞으로 이동시키는 거지만 실제 결과는 세번째 요소까지 즉 1,3,4가 정렬되었다. 그러므로 두번째 패스 (3을 두번째로 이동시키는 패스)와 세번째 패스(4를 세번째로 이동시키는 패스)는 필요 없는 패스가 된다. 그러므로 두번째 패스와 세번째 패스는 실행하지 않고 네번째 패스(6을 네번째로 이동시키는 패스)부터 다시 정렬을 시작하면 버블정렬의 시간복잡도를 더욱 향상 시킬 수 있다. 아래는 정렬이 완료 된 인덱스를 기록하여 정렬이 완료되지 않은 부분만 정렬하는 버블정렬이다.

 ```
 class Main {
    //가장 작은 수를 앞으로 옮기는 pass로 구성 된 버블 정렬
    //정렬이 완료된 인덱스를 기록하여 정렬이 완료되지 않은 부분만 정렬하는 업그레이드 버전
    private static int[] upgradedBubbleSort2(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        int j = 0;
        while(j<n-1){
            System.out.println("---"+"pass"+passCount+"---");
            int firstIndex = n-1;
            for(int i=n-1; i>j; i--){
                if(a[i] <  a[i-1]){
                    swap(a, i, i-1);
                    swapCount++;
                    firstIndex = i;
                }
                compareCount++;

            }
            printArray(a);
            passCount++;
            j = firstIndex;

        }

        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass로 구성 된 버블 정렬
    //정렬이 완료된 인덱스를 기록하여 정렬이 완료되지 않은 부분만 정렬하는 업그레이드 버전
    private static int[] upgradedBubbleSort3(int[] a, int n){
        int passCount = 0;
        int swapCount = 0;
        int compareCount = 0;
        int j = n-1;
        while(j>0){
            System.out.println("---"+"pass"+passCount+"---");
            int lastIndex = 0;
            for(int i=0; i<j; i++){
                if(a[i] > a[i+1]){
                    swap(a, i, i+1);
                    swapCount++;
                    lastIndex = i;
                }
                compareCount++;
            }
            printArray(a);
            passCount++;
            j = lastIndex;

        }

        System.out.println("passCount: " + passCount + ", compareCount: " + compareCount + ", swapCount: " + swapCount);
        return a;
    }

    private static void printArray(int[] a){
        for(int tmp : a){
            System.out.print(tmp+" ");
        }
        System.out.println();
    }

    private static void swap(int a[], int i0, int i1){
        int tmp = a[i0];
        a[i0] = a[i1];
        a[i1] = tmp;
    }

    public static void main(String[] args) {

        int[] array;

        array = new int[]{1, 3, 9, 4, 7, 8, 6};
        System.out.println("===array6 before bubbleSort0===");
        printArray(array);
        bubbleSort0(array, array.length);
        System.out.println("===array6 after bubbleSort0===");
        printArray(array);

        System.out.println();

        array = new int[]{1, 3, 9, 4, 7, 8, 6};
        System.out.println("===array7 before upgradedBubbleSort2===");
        printArray(array);
        upgradedBubbleSort2(array, array.length);
        System.out.println("===array7 after upgradedBubbleSort2===");
        printArray(array);

        System.out.println();

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array8 before bubbleSort1===");
        printArray(array);
        bubbleSort1(array, array.length);
        System.out.println("===array8 after bubbleSort1===");
        printArray(array);

        System.out.println();

        array = new int[]{6, 4, 3, 7, 1, 9, 8};
        System.out.println("===array9 before upgradedBubbleSort3===");
        printArray(array);
        upgradedBubbleSort3(array, array.length);
        System.out.println("===array9 after upgradedBubbleSort3===");
        printArray(array);
    }

}

-----출력 결과-----
===array6 before bubbleSort0===
1 3 9 4 7 8 6 
---pass0---
1 3 4 9 6 7 8 
---pass1---
1 3 4 6 9 7 8 
---pass2---
1 3 4 6 7 9 8 
---pass3---
1 3 4 6 7 8 9 
---pass4---
1 3 4 6 7 8 9 
---pass5---
1 3 4 6 7 8 9 
passCount: 6, compareCount: 21, swapCount: 6
===array6 after bubbleSort0===
1 3 4 6 7 8 9 

===array7 before upgradedBubbleSort2===
1 3 9 4 7 8 6 
---pass0---
1 3 4 9 6 7 8 
---pass1---
1 3 4 6 9 7 8 
---pass2---
1 3 4 6 7 9 8 
---pass3---
1 3 4 6 7 8 9 
passCount: 4, compareCount: 12, swapCount: 6
===array7 after upgradedBubbleSort2===
1 3 4 6 7 8 9 

===array8 before bubbleSort1===
6 4 3 7 1 9 8 
---pass0---
4 3 6 1 7 8 9 
---pass1---
3 4 1 6 7 8 9 
---pass2---
3 1 4 6 7 8 9 
---pass3---
1 3 4 6 7 8 9 
---pass4---
1 3 4 6 7 8 9 
---pass5---
1 3 4 6 7 8 9 
passCount: 6, compareCount: 21, swapCount: 8
===array8 after bubbleSort1===
1 3 4 6 7 8 9 

===array9 before upgradedBubbleSort3===
6 4 3 7 1 9 8 
---pass0---
4 3 6 1 7 8 9 
---pass1---
3 4 1 6 7 8 9 
---pass2---
3 1 4 6 7 8 9 
---pass3---
1 3 4 6 7 8 9 
passCount: 4, compareCount: 14, swapCount: 8
===array9 after upgradedBubbleSort3===
1 3 4 6 7 8 9 
 ```
실행결과를 보면 bubbleSort0과 upgradedBubbleSort2를 비교해 보았고 bubbleSort1과 upgradedBubbleSort3를 비교해보았다. 실행 결과 swapCount는 같지만 불필요한 패스를 실행하지 않으므로써 passCount와 compareCount가 줄어든 것을 확인 할 수 있다.