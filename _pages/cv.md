---
layout:    page
permalink: "/cv"
author:    jdecent
keywords:  about person demo example
title:     CurriculumVitae
menutitle: CV
weight:    70
excerpt:   This page contains the curriculum vitae (CV) of the author.
---
{% assign cv = site.data.cv[page.author] %}

<div class="md-card no-border">
    <p>{{cv.profile}}</p>
</div>

<div class="md-card shadow">
    <div class="title icon-stats-bars">
        <h2>技能</h2>
    </div>
    <div class="content">
        <ul>
            {% for skill in cv.skills %}
            <li>{{ skill }}</li>
            {% endfor %}
        </ul>
    </div>
</div>

<div class="md-card shadow education">
    <div class="title icon-library">
        <h2>教育经历</h2>
    </div>
    {% for entry in cv.education %}   
    <dl>
        <dt class="time">{{entry.time}}</dt>
        <dd>
            <h3>{{entry.location}}</h3>
            <p>{{entry.description}}</p>
        </dd>
    </dl>
    {% endfor %}
</div>

<h2 class="employment-heading">实习经历</h2>

<div class="timeline-container">
    {% for employment in cv.employment %}
    <div class="timeline-block">
        <div class="marker"></div>
        <div class="timeline-content">
            <div class="time">{{employment.time}}</div>
            <h3>{{employment.position}}</h3>
            <span>{{employment.company}} ({{employment.location}})</span>
            <ul>
                {% for responsibility in employment.responsibilities %}
                <li>{{responsibility}}</li>
                {% endfor %}
            </ul>
        </div>
    </div>
    {% endfor %}
</div>

