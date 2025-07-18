jQuery(function ($) {
'use strict';

    var init;
    var metrics_visible = false;
    var current_tab_id = null;

    var article_doi = null;
    var article_edps_ref = null;
    var siq_loaded = false;
    var alm_loaded = false;
    var article_content = null;
    var metrics_tab = null;
    var gaq = [];

    var track = function (action, label) {
        if (window._gaq && window._gaq !== gaq) {
            gaq.forEach(function (item) {window._gaq.push(item)});
            gaq = window._gaq;
        }
        try {
            gaq.push(['_trackEvent', 'metrics', action, label]);
            gaq.push(['t2._trackEvent', 'metrics', action, label]);
        } catch (e) {
        	console.log('track error', e, action, label)
        }
    };

    var load_siq = function (panel) {
        var iframe = $('<iframe />');
        iframe.prop('src', 'https://cm.scholarlyiq.com/Clients/EDP/en/ArticleCompleteView.htm?publisher=EDP&username=edpcharts&hashedPassword=fJBK0E87u%252BAU3rBxHe%2FQTzwFRP4%3D&style=https://www.edpsciences.org/images/metrics.css&article=' + article_edps_ref);
        iframe.css({
            width: "710px",
            height: "1160px",
            border: "0",
            display: "none"
        });
        iframe.on('load', function () {
            iframe.show();
            track('iframe-loaded-siq', article_doi);
        });
        panel.append(iframe);
    };

    var load_alm = function (panel) {
        $.ajax('https://api.altmetric.com/v1/doi/' + article_doi, {
            dataType: "json",
            success: function (data) {
                var iframe = $('<iframe />');
                iframe.prop('src', data.details_url);
                iframe.css({
                    width: "710px",
                    height: "1160px",
                    border: "0",
                    display: "none"
                });
                iframe.on('load', function () {
                    iframe.show();
                    track('iframe-loaded-alm', article_doi);
                });
                panel.append(iframe);
            }
        });
    };

    var load_tab_content = function () {
        if (!metrics_visible) return false;
        var filter = function (_, panel) {
            try {
                return panel.dataset['for'] === current_tab_id;
            } catch (e) {
                return $(panel).data('for') === current_tab_id;
            }
        };
        var target_panel = $('#metrics-tabs').find('.panel').filter(filter);
        if (!siq_loaded && current_tab_id === 'metrics-siq') {
            siq_loaded = true;
            load_siq(target_panel);
        }
        if (!alm_loaded && current_tab_id === 'metrics-alm') {
            alm_loaded = true;
            load_alm(target_panel);
        }
        return true;
    };

    var change_tab = function (tab_id, do_not_track) {
        if (tab_id === current_tab_id) return false;
        if (!do_not_track)
        	track('change-tab ' + tab_id, article_doi);
        if (tab_id === "return") return hide_metrics();
        current_tab_id = tab_id;
        var metrics_tabs = $('#metrics-tabs');
        var panels = metrics_tabs.find('.panel');
        var filter = function (_, panel) {
            return panel.dataset['for'] === tab_id;
        };
        var target_panel = panels.filter(filter);
        panels.not(target_panel).hide();
        target_panel.show();
        var buttons = metrics_tabs.find('nav.toolbar button.toolbar-item');
        var target_button = buttons.filter(function (_, button) {
            return button.id === tab_id;
        });
        buttons.not(target_button).removeClass('active');
        target_button.addClass('active');
        load_tab_content();
        return true;
    };

    var select_tab = function (event) {
        change_tab(event.target.id);
    };

    var show_metrics = function (event) {
        track('show', article_doi);
        article_content.hide();
        metrics_tab.show();
        metrics_visible = true;
        load_tab_content();
        event.preventDefault();
    };

    var hide_metrics = function () {
        metrics_visible = false;
        article_content.show();
        metrics_tab.hide();
    };

    init = function () {
        article_content = $('#article').add('.first_page');
        metrics_tab = $('#metrics-tabs');
        metrics_tab.hide();
        article_doi = metrics_tab.data('doi');
        article_edps_ref = metrics_tab.data('edps_ref');
        $('#show-metrics').click(show_metrics);
        metrics_tab.find('nav.toolbar button.toolbar-item').click(select_tab);
        change_tab('metrics-siq', true);
    };

init();

});
