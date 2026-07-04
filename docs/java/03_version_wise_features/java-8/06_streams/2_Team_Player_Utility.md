---
title: Team Utility
parent: Stream API
nav_order: 2
---

# Team and Player Stream Examples

These examples use Java 8 streams.

---

## Prepare Players List

```java
List<Team> teams = Team.getTeams();

List<Player> players = teams.stream()
    .flatMap(team -> team.getPlayers().stream())
    .collect(Collectors.toList());
```

---

## 1. Get All Unique Roles

```java
List<String> distinctRoles = players.stream()
    .map(Player::getRole)
    .distinct()
    .collect(Collectors.toList());
```

Better if you only need uniqueness:

```java
Set<String> roles = players.stream()
    .map(Player::getRole)
    .collect(Collectors.toSet());
```

Edge case:

`Collectors.toSet()` does not guarantee order.

Use `LinkedHashSet` if order matters.

```java
Set<String> roles = players.stream()
    .map(Player::getRole)
    .collect(Collectors.toCollection(LinkedHashSet::new));
```

---

## 2. Top 2 Players By Strike Rate

```java
List<Player> top2Players = players.stream()
    .sorted(Comparator.comparingDouble(Player::getStrikeRate).reversed())
    .limit(2)
    .collect(Collectors.toList());
```

Edge case:

If players list has fewer than 2 players, it returns whatever is available. No exception.

---

## 3. Partition Players By Age

```java
Map<Boolean, List<Player>> partition = players.stream()
    .collect(Collectors.partitioningBy(player -> player.getAge() > 35));
```

Result:

- `true` -> players older than 35.
- `false` -> players age 35 or below.

---

## 4. Total Score Of All Players

```java
int totalScore = players.stream()
    .mapToInt(Player::getTotalScore)
    .sum();
```

---

## 5. Join All Player Names

```java
String names = players.stream()
    .map(Player::getName)
    .collect(Collectors.joining(", "));
```

Edge case:

If any player name is null, joining can throw `NullPointerException`.

Safer:

```java
String names = players.stream()
    .map(Player::getName)
    .filter(Objects::nonNull)
    .collect(Collectors.joining(", "));
```

---

## 6. Average Score Per Role

```java
Map<String, Double> avgScoreByRole = players.stream()
    .collect(Collectors.groupingBy(
        Player::getRole,
        Collectors.averagingDouble(Player::getTotalScore)
    ));
```

---

## 7. Highest Strike Rate Player Per Role

```java
Map<String, Optional<Player>> highestStrikeByRole = players.stream()
    .collect(Collectors.groupingBy(
        Player::getRole,
        Collectors.maxBy(Comparator.comparingDouble(Player::getStrikeRate))
    ));
```

Why Optional?

A group may theoretically be empty in some collector combinations, so `maxBy` returns Optional.

---

## 8. Team With Highest Total Score

```java
Optional<Team> topTeam = teams.stream()
    .max(Comparator.comparingInt(team ->
        team.getPlayers().stream()
            .mapToInt(Player::getTotalScore)
            .sum()
    ));
```

Edge case:

If `teams` is empty, result is `Optional.empty()`.

Do not call `topTeam.get()` without checking.

---

## 9. Sort Players By Team Ranking, Then Strike Rate

Java 8 does not have `Map.entry`, so use `AbstractMap.SimpleEntry`.

```java
List<Player> sortedPlayers = teams.stream()
    .flatMap(team -> team.getPlayers().stream()
        .map(player -> new AbstractMap.SimpleEntry<Team, Player>(team, player)))
    .sorted(
        Comparator
            .comparingInt((AbstractMap.SimpleEntry<Team, Player> entry) ->
                entry.getKey().getRanking())
            .thenComparing(
                entry -> entry.getValue().getStrikeRate(),
                Comparator.reverseOrder()
            )
    )
    .map(AbstractMap.SimpleEntry::getValue)
    .collect(Collectors.toList());
```

Sort logic:

1. Lower team ranking first.
2. Higher strike rate first inside same ranking.

---

## 10. Top Scorer Per Team And Role

```java
Map<String, Map<String, Player>> result = teams.stream()
    .collect(Collectors.toMap(
        Team::getName,
        team -> team.getPlayers().stream()
            .collect(Collectors.groupingBy(
                Player::getRole,
                Collectors.collectingAndThen(
                    Collectors.maxBy(Comparator.comparingInt(Player::getTotalScore)),
                    Optional::get
                )
            ))
    ));
```

Edge case:

`Optional::get` is safe here only because each group created by `groupingBy` has at least one player.

If data can be unusual or manually created, handle Optional more carefully.

---

## 11. For Each Team: Total Score And Average Strike Rate

Java 8 does not support records, so use a normal class.

```java
class TeamStats {
    private final double averageStrikeRate;
    private final int totalScore;

    TeamStats(double averageStrikeRate, int totalScore) {
        this.averageStrikeRate = averageStrikeRate;
        this.totalScore = totalScore;
    }

    public double getAverageStrikeRate() {
        return averageStrikeRate;
    }

    public int getTotalScore() {
        return totalScore;
    }
}
```

```java
Map<String, TeamStats> teamStats = teams.stream()
    .collect(Collectors.toMap(
        Team::getName,
        team -> {
            int totalScore = team.getPlayers().stream()
                .mapToInt(Player::getTotalScore)
                .sum();

            double avgStrikeRate = team.getPlayers().stream()
                .mapToDouble(Player::getStrikeRate)
                .average()
                .orElse(0.0);

            return new TeamStats(avgStrikeRate, totalScore);
        }
    ));
```

---

## Quick Summary

These examples show common Java 8 stream patterns: `flatMap`, `distinct`, sorting, partitioning, grouping, joining, numeric streams, `Optional`, and collectors.

