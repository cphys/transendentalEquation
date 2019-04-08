In order to find all of the roots for $x$ in range $\{0,1\}$, (without placing a limit on the range of $a$), you should use [reduce](https://reference.wolfram.com/language/ref/Reduce.html). Because the user specifically wrote the function as $\sin(a)/a = z$ we do not convert the equation to $\text{sinc}(a)$, and specifically exclude the point $a=0$ because this would put a zero in the denominator. 

    f[yMax_, x_] := f[yMax, x] =
        If[x != 1,
            If[x != 0 ,
                {ToRules[N[Reduce[Sin[a]/a == x, a, Reals]]]},
                DeleteCases[Flatten[Table[ FullSimplify[Solve[Sin[a]/a == 0, a, Reals], a != 0 && C[1] \[Element] Integers] /. C[1] -> iConst, {iConst, -IntegerPart[yMax],  IntegerPart[yMax]}], 1], {a -> 0}]],
    {}]

Writing all of the solutions for a particular value of x, as an array of ordered pairs gives,

    finalFunction[yMax_, x_] := If[f[yMax, x] != {},
        {x, a} /. f[yMax, x],
        Nothing]

We can list all of the roots for values $x$ in the range $\{0,1\}$ to an arbitrary resolution in values of $x$ using the function,

    listAllRoots[yMax_, resolution_] := listAllRoots[yMax, resolution] = SortBy[Flatten[ParallelTable[N[finalFunction[yMax, x]], {x, -1,1,1/resolution}], 1], Last]

Plotting all of these values at different scales of $a(x)$ using the function,

    finalPlot[yMax_, resolution_] := ListLinePlot[
        listAllRoots[yMax,resolution],
        AspectRatio -> .75,
        PlotRange -> {{Automatic,1.0554}, {-yMax - .1*yMax, +yMax + .1*yMax}},
        LabelStyle -> {FontFamily -> "Latex", FontSize -> 25},
        FrameLabel -> {"x", "a(x)"},
        FrameTicks -> {{Table[Round[i, 1], {i, -yMax, yMax, yMax/3}], None},{Automatic, None}},
        PlotTheme -> "Scientific", ImageSize -> 450]

Here we have plotted all of the roots to the transcendental equation. Note that at $x=0$ the full solution is $a=n\pi$ where $n$ is any positive or negative integer. Thus there are infinite solutions at $x=0$, so here we have used the parameter $\text{yMax}$ to only solve for values $a=\{-\text{yMax},\text{yMax}\}$, which are within the plotting window. This value may be arbitrarily adjusted to any value. 

    GraphicsRow[{finalPlot[3, 500], finalPlot[30, 500], finalPlot[90, 500]}]

[![enter image description here][1]][1]


  [1]: https://i.stack.imgur.com/QK7oc.png
