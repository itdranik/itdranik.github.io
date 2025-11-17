---
_edit_last: "2"
_last_editor_used_jetpack: block-editor
_thumbnail_id: "1734"
_wpas_done_all: "1"
author: yauheni.shybeka
categories:
  - coding-interview
cover:
  alt: max-points-on-a-line-feature-image
image: /wp-content/uploads/2020/07/max-points-on-a-line-feature-image.jpg
date: "2020-07-07T05:43:26+00:00"
guid: https://itdranik.com/?p=1653
language:
  - English
parent_post_id: null
post_id: "1653"
post_translations:
  - pll_5e4809c57cc17
tags:
  - c#
  - geometry
title: 'Problem: Max Points on a Line'
url: /en/problem-max-points-on-a-line-en/

---
The hot sun lit the bedroom. Bright rays ran through the face of the senior developer Vasily. “Just five more minutes,” a cunning hope slipped into Vasily’s head, and he stretched out in a satisfied smile until he suddenly realized that he had overslept. Another day in a row he woke up with a mobile phone in his hand and a missed alarm. This was really annoying, but Vasily's cunning body did not take his problems seriously and personally made the decision to turn off the alarm before Vasily could even understand what was happening. There was no time to deal with it as the next important meeting was about to begin. For the whole week, the team had not yet decided where it was better to have a very important button. The need for personal presence at the meeting was not fully understood by Vasily, as well as the reason for such a painful choice. But, as a backend developer, he had to confirm constantly, "If the button is ten pixels to the right, this won't break the backend."

Vasily more and more understood that he did not have enough time for the development itself, therefore more and more he leafed through LinkedIn, visited different meetups, and played board games with strangers in search of new developers for his team. Our editorial staff managed to get one of the tasks that Vasya offers as a test. Therefore, we propose to deal with the solution together, suddenly Vasily will write to you too!

**Problem Statement:** Given a set of points on a plane, find the line that passes through the maximum number of points.

The complete implementation in C#, as usual, can be found on the [GitHub](https://github.com/itdranik/coding-interview/) of the coding-interview project.

Since there is an infinite number of lines on the plane, it can take forever to find an answer, while your interviewer is a very busy person, he is participating today in a team meeting on choosing the name and color of a button. Therefore, we will try to accelerate and optimize the approach.

Let's limit the set of lines that can potentially be the answer. If the set of points is empty, then any line will be the answer. For example, you can return a line passing through the center of coordinates.

A line is uniquely defined by two different points, so we will sort through all possible pairs of different points, build a line from them, and check that the points belong to this line.

We work with real coordinates, so the comparison of points will be carried out with some accuracy, hence we have the names of the methods `AllPointsAreAlmostEqual`, `IsAlmostEqual` and so on. For now, we will omit the implementation of geometric methods and dwell on the implementation of the body of the algorithm:

The algorithm has a cubic complexity `O(n^3)`: we need to go through all the possible pair of points `O(n^2)`, build a line based on these points `O(1)` and check the belonging of other points to this line `O(n)`.

Let's consider the case when three points lie on one line:

{{< figure src="/wp-content/uploads/2020/07/MaxPointsOnALine-OneLine.png" alt="Three points on a line" caption="Three points on a line" >}}

Fix a point A as the first point of the line. Our implemented algorithm will first construct a line using the pair of points (A, B) and verify that the points belong to this line, and then for the pair of points (A, C). But it is one and the same line! Moreover, if the pair of points (A, B) and the pair of points (A, C) form the same line, then it means that all three points lie on this line.

Since we have fixed the first point (A) of a line, we can say that all lines pass through this point, which means that they can be uniquely defined by the direction vector of the line. Moreover, collinear vectors will correspond to the same line:

{{< figure src="/wp-content/uploads/2020/07/MaxPointsOnALine-CollinearVectors.png" alt="Collinear vectors" caption="Collinear vectors" >}}

Thus, for each fixed point, it is necessary to find the direction (some normalized vector) which is collinear to the largest number of vectors formed by a pair of different points.

Let's introduce temporarily a constraint for the coordinates of the points. Let our points have only integer coordinates. Then the coordinates of every direction vector will be also integer. Then, to normalize the coordinates, we divide both coordinates by their greatest common divisor. This vector is not normalized in the mathematical sense, but it allows us to use it as a reference for every vector collinear to the given one. For example, the vectors (4, 2) and (-2, -1) are converted to the vector (2, 1).

{{< figure src="/wp-content/uploads/2020/07/MaxPointsOnALine-NormalizedVectors.png" alt="Normalized vector" caption="Normalized vector" >}}

Based on the foregoing, we will construct an improved implementation for integer coordinates. Let's define the body of the main method. For each fixed point, we will find the direction with the largest number of collinear vectors:

And we'll implement the algorithm for finding the best direction for some selected fixed point:

```
private (Vector<long> Direction, int PointsCount) FindDirectionWithMaxPointsCount(
    Point<long> origin,
    List<Point<long>> points)
{
    int originDuplicates = 0;
    var pointsCountPerDirection = new Dictionary<long, Dictionary<long, int>>();
    int maxPointsCount = 0;
    Vector<long> bestDirection = new Vector<long>(1, 1);

    foreach (var linePoint in points)
    {
        if (linePoint.IsEqual(origin))
        {
            ++originDuplicates;
            continue;
        }

        var direction = NormalizeDirection(
            VectorsFactory.BuildVector(origin, linePoint)
        );
        var pointsCount = AddDirectionAndGetPointsCount(
            pointsCountPerDirection,
            direction
        );

        if (pointsCount > maxPointsCount)
        {
            maxPointsCount = pointsCount;
            bestDirection = direction;
        }
    }

    return (Direction: bestDirection, PointsCount: maxPointsCount + originDuplicates);
}
```

We will implement the building of a normalized vector and counting the number of vectors collinear to it. To do this, let's create an associative two-dimensional array, where the keys will be the coordinates (X, Y) of a normalized vector, and the values will be the number of vectors collinear to this vector:

So we got an algorithm with the complexity `O(n^2)`, where for each fixed point `O(n)` the best direction is found in `O(n)`.

An important note to the algorithm above is the calculating of the greatest common divisor ( `GCD`). The resulting divider also rotates the vector to the desired quarter.

The algorithm above is designed to work with integer coordinates. Suppose that all our coordinates are set with some precision `digitsPrecision` (the number of significant digits after the decimal point). Then we can perform the calculations by multiplying all the coordinates by `multiplier = 10 ^ digitsPrecision`, and reduce the solution to the case described above. And we will receive the resulting answer by an inverse transformation (division by the multiplier):

```
public (Line<double> line, int PointsCount) FindLineFast(
    List<Point<double>> points,
    int digitsPrecision = DefaultDigitsPrecision)
{
    double multiplier = Math.Pow(10, digitsPrecision);

    var longPoints = points.Select((p) => new Point<long>(
        Convert.ToInt64(Math.Round(p.X * multiplier)),
        Convert.ToInt64(Math.Round(p.Y * multiplier))
    )).ToList();

    (var longOrigin, var longDirection, var pointsCount) = FindVectorFast(longPoints);

    var origin = new Point<double>(
        longOrigin.X / multiplier,
        longOrigin.Y / multiplier
    );
    var direction = new Vector<double>(
        longDirection.X / multiplier,
        longDirection.Y / multiplier
    );

    return (LinesFactory.BuildLine(origin, direction), pointsCount);
}
```

That's all. I need to say goodbye. And I'd like to ask you, dear reader, to familiarize yourself also with the other reviews of [coding-interview problems](/en/topics/coding-interview-en/). See you soon!
